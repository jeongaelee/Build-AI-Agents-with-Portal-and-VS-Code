# Step 05. 에이전트 연동 클라이언트 애플리케이션 준비

## 목표
실습 저장소를 복제하고 Python 클라이언트 코드를 준비합니다.

## 실습 순서
1. VS Code 명령 팔레트(Ctrl+Shift+P 또는 View > Command Palette)에서 Git: Clone을 실행합니다.

2. 아래 저장소 URL을 입력해 로컬에 Clone 합니다. 

```text
https://github.com/MicrosoftLearning/mslearn-ai-agents.git
```

3. Clone 완료 후 로컬 저장소를 VS Code로 엽니다.

4. File > Open Folder에서 아래 폴더를 선택합니다.

```text
mslearn-ai-agents/Labfiles/01-build-agent-portal-and-vscode/Python
```

5. 탐색기에서 `agent_with_functions.py` 파일을 열고, 파일이 비어 있으면 기존 내용을 아래 코드로 대체합니다.

```python
import base64
import os
from pathlib import Path

from azure.ai.projects import AIProjectClient
from azure.identity import DefaultAzureCredential
from dotenv import load_dotenv


OUTPUT_DIR = Path("agent_outputs")


def get_output_path(filename):
	"""Create a unique path for generated files."""
	OUTPUT_DIR.mkdir(exist_ok=True)
	file_name = Path(filename).name
	stem = Path(file_name).stem or "output"
	suffix = Path(file_name).suffix
	output_path = OUTPUT_DIR / file_name

	counter = 1
	while output_path.exists():
		output_path = OUTPUT_DIR / f"{stem}_{counter}{suffix}"
		counter += 1

	return output_path


def save_bytes(file_bytes, filename):
	"""Save binary content to a local file."""
	output_path = get_output_path(filename)
	with open(output_path, "wb") as file_handle:
		file_handle.write(file_bytes)
	return output_path


def save_image(image_data, filename):
	"""Save base64 image data to a file."""
	return save_bytes(base64.b64decode(image_data), filename)


def download_container_file(openai_client, annotation, downloaded_files):
	"""Download a cited container file once and return its local path."""
	cache_key = (annotation.container_id, annotation.file_id)
	if cache_key in downloaded_files:
		return downloaded_files[cache_key]

	file_content = openai_client.containers.files.content.retrieve(
		file_id=annotation.file_id,
		container_id=annotation.container_id,
	)
	output_path = save_bytes(
		file_content.read(),
		annotation.filename or f"{annotation.file_id}.bin",
	)
	downloaded_files[cache_key] = output_path
	return output_path


def format_output_text(content_item, openai_client, downloaded_files):
	"""Replace sandbox file citations with local file paths."""
	text = content_item.text or ""
	replacements = []
	referenced_files = set()

	for annotation in content_item.annotations or []:
		if getattr(annotation, "type", "") != "container_file_citation":
			continue

		output_path = download_container_file(openai_client, annotation, downloaded_files)
		replacement_text = f"{annotation.filename} (saved to {output_path})"
		referenced_files.add(output_path)

		start_index = getattr(annotation, "start_index", None)
		end_index = getattr(annotation, "end_index", None)
		if start_index is not None and end_index is not None:
			replacements.append((start_index, end_index, replacement_text))
			continue

		annotated_text = getattr(annotation, "text", "")
		if annotated_text:
			text = text.replace(annotated_text, replacement_text)

	for start_index, end_index, replacement_text in sorted(replacements, reverse=True):
		text = f"{text[:start_index]}{replacement_text}{text[end_index:]}"

	return text, referenced_files


def main():
	# Initialize the project client
	load_dotenv()
	project_endpoint = os.environ.get("PROJECT_ENDPOINT")
	agent_name = os.environ.get("AGENT_NAME", "it-support-agent")

	if not project_endpoint:
		print("Error: PROJECT_ENDPOINT environment variable not set")
		print("Please set it in your .env file or environment")
		return

	print("Connecting to Microsoft Foundry project...")
	credential = DefaultAzureCredential()
	project_client = AIProjectClient(
		credential=credential,
		endpoint=project_endpoint
	)

	# Get the OpenAI client for Responses API
	openai_client = project_client.get_openai_client()

	# Get the agent created in the portal
	print(f"Loading agent: {agent_name}")
	agent = project_client.agents.get(agent_name=agent_name)
	print(f"Connected to agent: {agent.name} (id: {agent.id})")

	# Create a conversation
	conversation = openai_client.conversations.create(items=[])
	print(f"Conversation created (id: {conversation.id})")

	# Chat loop
	print("\n" + "=" * 60)
	print("IT Support Agent Ready!")
	print("Ask questions, request data analysis, or get help.")
	print("Type 'exit' to quit.")
	print("=" * 60 + "\n")

	while True:
		user_input = input("You: ").strip()

		if user_input.lower() in ["exit", "quit", "bye"]:
			print("Goodbye!")
			break

		if not user_input:
			continue

		# Add user message to conversation
		openai_client.conversations.items.create(
			conversation_id=conversation.id,
			items=[{"type": "message", "role": "user", "content": user_input}]
		)

		# Get response from agent
		print("\n[Agent is thinking...]")
		response = openai_client.responses.create(
			conversation=conversation.id,
			extra_body={"agent_reference": {"name": agent.name, "type": "agent_reference"}},
			input=""
		)

		# Display response and save any generated files locally
		handled_output = False
		downloaded_files = {}
		referenced_files = set()
		image_count = 0

		if hasattr(response, "output") and response.output:
			for item in response.output:
				item_type = getattr(item, "type", "")

				if item_type == "message" and getattr(item, "content", None):
					for content_item in item.content:
						if getattr(content_item, "type", "") != "output_text":
							continue

						formatted_text, message_files = format_output_text(
							content_item,
							openai_client,
							downloaded_files,
						)
						referenced_files.update(message_files)

						if formatted_text:
							print(f"\nAgent: {formatted_text}\n")
							handled_output = True

				elif hasattr(item, "text") and item.text:
					print(f"\nAgent: {item.text}\n")
					handled_output = True

				elif item_type == "image":
					image_count += 1
					filename = f"chart_{image_count}.png"

					if hasattr(item, "image") and hasattr(item.image, "data"):
						file_path = save_image(item.image.data, filename)
						print(f"\n[Agent generated a chart - saved to: {file_path}]")
					else:
						print("\n[Agent generated an image]")
					handled_output = True

			for file_path in downloaded_files.values():
				if file_path not in referenced_files:
					print(f"\n[Agent generated a file - saved to: {file_path}]")
					handled_output = True

		if not handled_output and hasattr(response, "output_text") and response.output_text:
			print(f"\nAgent: {response.output_text}\n")


if __name__ == "__main__":
	main()
```

9. agent_with_functions.py 파일을 저장합니다.

## 다음 단계

* [Step 06. 환경 구성 후 애플리케이션 실행](step06.md)

## 실습 순서

* [개요. Build AI Agents with Portal and VS Code](README.md)
* [Step 01. Microsoft Foundry 프로젝트와 에이전트 생성](step01.md)
* [Step 02. 에이전트 지시문과 그라운딩 데이터 구성](step02.md)
* [Step 03. 포털에서 에이전트 테스트](step03.md)
* [Step 04. VS Code에서 에이전트 연결 및 테스트](step04.md)
* [Step 05. 에이전트 연동 클라이언트 애플리케이션 준비](step05.md)
* [Step 06. 환경 구성 후 애플리케이션 실행](step06.md)
* [Step 07. 클라이언트 테스트 및 정리(Cleanup)](step07.md)