# Bangla Book RAG Chatbot

**Ostad Module 26 Assignment: Knowledge Base Chatbot with Vector Database**

A Bengali-language question-answering chatbot based on the book **স্পেনীয় মুসলমান সভ্যতা** by **সৈয়দ ইসমাইল হোসেন সিরাজী**. The chatbot retrieves relevant passages from the book and uses Google Gemini to answer in Bengali with chapter-level source links. When the book does not support an answer, it returns a fixed no-answer message.

## Source book

- **Title:** স্পেনীয় মুসলমান সভ্যতা
- **Author:** সৈয়দ ইসমাইল হোসেন সিরাজী
- **Source:** [Bengali Wikisource](https://bn.wikisource.org/wiki/স্পেনীয়_মুসলমান_সভ্যতা)
- **Coverage:** 11 chapter pages, retrieved from Wikisource when the notebook runs

## How it works

1. **Collect:** Download the book's 11 chapters with `requests` and Beautiful Soup.
2. **Prepare:** Clean the text and split it into overlapping chunks (chunk size **800**, overlap **120**; **90 chunks** in the recorded run). Preserve chapter names and source URLs as metadata.
3. **Embed and index:** Generate multilingual embeddings with `sentence-transformers/paraphrase-multilingual-MiniLM-L12-v2` and store them in a FAISS vector index.
4. **Retrieve:** Combine semantic search (FAISS) with character n-gram TF-IDF retrieval to find relevant Bengali passages.
5. **Answer:** Pass the retrieved context to Google Gemini (`gemini-3.5-flash-lite`) through LangChain. If the initial response indicates no answer, the chatbot also tries the full text of the top-ranked chapter.
6. **Display:** Use a Gradio chat interface to show Bengali answers and linked chapter citations. For unsupported questions, return `এই বইয়ে প্রশ্নটির উত্তর পাওয়া যায়নি।` without citations.

## Repository files

```text
module26-bangla-book-rag/
├── Module26_Bangla_Book_RAG_Shah_Aymaan_Ibtihal.ipynb
├── README.md
└── requirements.txt
```

The notebook contains the full data collection, preprocessing, retrieval, chatbot, 10 test questions, and Gradio launch workflow.

## Run in Google Colab

1. Open `Module26_Bangla_Book_RAG_Shah_Aymaan_Ibtihal.ipynb` in **Google Colab**.
2. Select **Runtime → Run all**. The first cell installs the required Python packages; the remaining cells download the book and construct the in-memory vector index.
3. When prompted, enter your own **Google Gemini API key**. The notebook uses `getpass` and does not contain a hardcoded API key.
4. Review the printed results for the 10 test questions.
5. Open the Gradio interface displayed by the last cell and ask questions in Bengali.

You will need an internet connection to access Wikisource, download the embedding model, and call Gemini. The notebook creates the FAISS index at runtime rather than loading a committed index file. The Gradio public share URL is temporary and works only while the Colab session and app remain running.

## Test questions

The notebook defines and executes these 10 questions, along with expected answers and relevant chapter information.

| # | Question | Expected answer / behavior |
|---|---|---|
| 1 | কর্ডোভা নগরী কেন বিখ্যাত ছিল? | সভ্যতা, শিক্ষা, সৌন্দর্য, শিল্প ও বাণিজ্যের জন্য |
| 2 | কর্ডোভার জামে মসজিদের পত্তন কে করেন এবং কবে? | সোলতান প্রথম আব্দর রহমান, ৭৮৪ খ্রিস্টাব্দে |
| 3 | কর্ডোভার জামে মসজিদের নির্মাণ কে সম্পন্ন করেন? | সোলতান হিশাম, ৭৯৭ খ্রিস্টাব্দে |
| 4 | আজ্‌ জোহরা নগরী কে প্রতিষ্ঠা করেন? | খলিফা তৃতীয় আব্দর রহমান |
| 5 | কর্ডোভায় সাধারণের জন্য কতটি বিরাট লাইব্রেরি ছিল? | ১৭টি |
| 6 | খলিফা হাকেম কতটি মূল্যবান গ্রন্থ সংগ্রহ করেছিলেন? | প্রায় ছয় লক্ষ |
| 7 | আলহামরা প্রাসাদের ভিত্তি কে স্থাপন করেন? | সোলতান ইবনে আলআহমর |
| 8 | গ্রাণাডায় কোন কোন শিল্পের উন্নতি হয়েছিল? | কাগজ, রেশমী বস্ত্র, অস্ত্রশস্ত্র, খোদাই ও অন্যান্য শিল্প |
| 9 | গ্রাণাডায় কতটি কলেজ ও উচ্চশ্রেণীর বিদ্যালয় প্রতিষ্ঠিত হয়েছিল? | ২০০টি |
| 10 | ১৯৬৯ সালে চাঁদে প্রথম কে অবতরণ করেন? | এই বইয়ে প্রশ্নটির উত্তর পাওয়া যায়নি। |

All 10 tests executed in the saved notebook output. The answers were reviewed against the expected content; **the notebook does not compute an automated accuracy metric**. The original-source links accompanying answers make it possible to inspect their supporting chapters.

## Notes and limitations

- Responses depend on the text retrieved from the selected book, the external Gemini service, and the wording of the question.
- Wikisource formatting or availability may change, so chapter extraction depends on the website remaining accessible.
- Chapter citations are links to source pages rather than exact sentence-level references.
- The retrieval index is rebuilt when the notebook runs; it is not stored in the repository.

**Prepared by:** Shah Aymaan Ibtihal
