# codellama-python
This is a PoC about integrating docker + codellama + python in order to apply code review in the pre-commit git hook

# How it works
- First you should serve `ollama` using docker `docker run -d -v ollama:/root/.ollama -p 11434:11434 --name ollama ollama/ollama` .
- Clone this project and move inside it.
- Add or modify the python files.
- Apply a commit message and wait for the review. I could take some minutes, the final result is a readme.md with all suggestions from codellama.
