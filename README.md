LangChain 기반 LLM 챗봇 실습
LangChain과 OpenAI·Hugging Face 모델을 이용해 간단한 질의응답 챗봇 → PDF 요약 웹사이트 → PDF 기반 질의응답(RAG) 챗봇 → 대화형 PDF 챗봇까지 단계적으로 만들어 본 실습 모음입니다. 웹 화면은 Streamlit으로 구성했습니다. 
기술 스택
분류	사용 기술
LLM 프레임워크	LangChain (PromptTemplate, LLMChain, SequentialChain, ConversationChain, Agent)
모델	OpenAI `gpt-4o-mini`, Hugging Face `google/flan-t5-large`
임베딩	OpenAI Embeddings, `sentence-transformers/all-MiniLM-L6-v2`
벡터 DB	FAISS, Chroma
문서 처리	PyPDF2, PyPDFLoader, TextLoader, RecursiveCharacterTextSplitter
웹 UI	Streamlit, streamlit-chat
노트북 구성
파일	내용
`5_1_간단한_챗봇_만들기.ipynb`	첫 챗봇 실습 — Streamlit 입력창 + `gpt-4-0314`로 질문에 답하는 최소 구성 챗봇
`LLM1.ipynb`	LangChain 기본기 — 프롬프트 템플릿, OpenAI와 Hugging Face 모델 답변 비교(ModelLaboratory), PDF 로딩 후 FAISS 임베딩, LLMChain·SequentialChain(번역 → 요약), 대화 메모리(ConversationChain), Wikipedia·계산 도구를 쓰는 Agent
`LLM2.ipynb`	간단한 챗봇 — Streamlit 입력창에 질문하면 `gpt-4o-mini`가 답변
`LLM3.ipynb`	문서 기반 질의응답 — 텍스트를 청크로 나눠 Chroma에 저장하고, 유사 문서를 찾아 답변(load_qa_chain)
`LLM4.ipynb`	PDF 요약 웹사이트 — PDF 업로드 → 청크 분할 → FAISS 검색 → 3~5문장 요약, 호출 비용 확인(get_openai_callback)
`LLM5.ipynb`	PDF 질의응답 챗봇 — 여러 PDF 업로드, ConversationalRetrievalChain + 최근 대화 기억(ConversationBufferWindowMemory)
`대화형 챗봇.ipynb`	대화형 PDF 챗봇 — 업로드한 PDF 내용을 바탕으로 채팅 말풍선 UI(streamlit-chat)에서 이어서 대화
처리 흐름 (PDF 챗봇)
```
PDF 업로드 ─▶ 텍스트 추출 ─▶ 청크 분할 (1,000자, 200자 겹침)
          ─▶ 임베딩 ─▶ FAISS 벡터 저장소
질문 ─▶ 유사 청크 검색 ─▶ LLM(gpt-4o-mini)에 문맥과 함께 전달 ─▶ 답변 (대화 기록 유지)
```
실행 방법
```bash
pip install langchain langchain-openai langchain-community streamlit streamlit-chat openai pypdf PyPDF2 faiss-cpu sentence-transformers chromadb
```
노트북 안의 `os.environ["OPENAI_API_KEY"] = "sk"` 부분에 본인 OpenAI API 키를 넣습니다. (키는 저장소에 올리지 않습니다)
Streamlit 코드는 셀 내용을 `app.py`로 저장한 뒤 `streamlit run app.py`로 실행합니다.
`LLM1`, `LLM3`의 문서 경로는 로컬 경로이므로 본인 파일 위치로 바꿔야 합니다.
배운 점
긴 문서를 그대로 넣지 않고 청크 분할 → 임베딩 → 유사도 검색으로 필요한 부분만 LLM에 전달하는 RAG 구조
대화 메모리를 붙이면 앞의 대화 내용을 기억해 이어서 답변할 수 있다는 점
LangChain 버전이 바뀌면서 `LLMChain`, `initialize_agent` 등이 deprecated 되는 등, 라이브러리 버전 관리의 중요성
