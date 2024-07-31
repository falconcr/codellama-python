# codellama-python
This is a PoC about integrating docker + codellama + python in order to apply code review in the pre-commit git hook

# How it works
- First you should serve `ollama` using docker `docker run -d -v ollama:/root/.ollama -p 11434:11434 --name ollama ollama/ollama` .
- Clone this project and move inside it.
- Create the following file `.git/hooks/pre-commit`
```
  #!/bin/sh

# Find all modified Python files
FILES=$(git diff --cached --name-only --diff-filter=ACM | grep '\.py$')

if [ -z "$FILES" ]; then
  echo "No Python files to check."
  exit 0
fi

# Clean the review.md file before starting
> review.md

# Review each modified Python file
for FILE in $FILES; do
  content=$(cat "$FILE")
  prompt="\n Review this code, provide suggestions for improvement, coding best practices, improve readability, and maintainability. Remove any code smells and anti-patterns. Provide code examples for your suggestion. Respond in markdown format. If the file does not have any code or does not need any changes, say 'No changes needed'."
  
  # get model review suggestions
  suggestions=$(docker exec ollama ollama run codellama "Code: $content $prompt")
  
  # Añadir el prefijo del nombre del archivo y las sugerencias al archivo review.md
  echo "## Review for $FILE" >> review.md
  echo "" >> review.md
  echo "$suggestions" >> review.md
  echo "" >> review.md
  echo "---" >> review.md
  echo "" >> review.md
done

echo "All Python files were applied the code review."
exit 0
```
- Add or modify the python files.
- Apply a commit message and wait for the review. I could take some minutes, the final result is a readme.md with all suggestions from codellama.
