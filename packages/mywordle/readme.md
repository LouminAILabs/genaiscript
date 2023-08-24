# Exploring CoArch with the mywordle Sample

# Prompts

There are 5 general prompts in this directory that are intended to reflect 4 personas associated with software development.  These prompts appear in the "appdev" folder of the prompts menu.

- **PM-planning.prompt.js** - This prompt takes a project description in a .coarch.md file (a paragraph describing the application), and expands the contents of that file to include specific instructions for the software architect, the software developer, and the quality assurance engineer on how to implement the project.
- **SA-planning.js** - This prompt takes the contents of the planning file generated by the PM-planning prompt (blackjack.coarch.md) and generates a new CoArch file (blackjack.saplan.coarch.md) with detailed instructions for the software developer.  It expands the general guidance to include specific aspects of the appliction, including what files it should contain and how they interact.
- **SDE-Coding** - This prompt takes a detailed explanation of the application, including descriptions of the files and the interfaces between them, and generates Python code to implement the application.
- **SDE-update** - This prompt is intended to take a modified architecture file and make changes only to the corresponding implementation files that require changes.
- **QA-coding.js** - This prompt takes the contents of the blackjack.saplan.coarch.md, and generates a set of test files that test the components of the application as specified by the architect.

# CoArch Workflow

We start the workflow from a simple heading and short description. In this example, we are building a command line blackjack game but the prompts are general and can work for other applications as well.  Here is the starting point for the blackjack application.

```
# Create a command-line version of the popular Wordle application

## Idea
This application implements Wordle as a command line application. The rules follow the rules of the popular game. The game picks a random hidden 5-letter word from a dictionary of legal words. The user provides a 5-letter word and the game shows the user which of the letters in their word is (1) in the hidden word and (2) whether that letter is in the correct position. The user then offers another 5-letter word. 
If the users' word is an illegal word, the game should tell the user it is illegal, give no feedback except to
request another word until they type a legal word.
They have a total of 6 guesses. Illegal guess don't count against their total.
If they guess the hidden word they win. If they lose, the game should show them the hidden word. The game should use graphical elements similar to the Web-based version to show which of their letters was in the hidden word or in the correct location.
After each turn the user feedback should include exactly what letters are correct and if letters appear in the correct position, 
it should say what position they are in.  The game should also print out the hidden word at the end when the user quits.
```

Generation steps:
1. Place the description above in ```game.coarch.md```
2. Run the PM-planning prompt over the file to expand the description.
3. Run the SA-planning prompt over the same file to generate the file ```game.saplanning.coarch.md```
4. Run the SDE-planning prompt over the saplanning file to generate a set of Python files that implement the application.
5. Test the application, edit the files as needed, and rerun the prompts as needed.
6. To create unit tests for the python files generated for the application, 
run the QA-coding prompt over the ```game.saplanning.coarch.md``` file.  This should
generate parallel test files to the previous .py files as well as a file that can run all the tests from the command line.  (not yet implemented)

# Running the application

The Python files in the application directory were generated using the process above and edited either by hand or by rewriting the architecture document and rerunning the SDE prompts. The file 
main.py in the sample directory was generated using the process above and it implements a command line version of Wordle.  You run it with:
```python main.py```