# Approach
I have broken down my code into small parts to implement the function. The implementation can be divided into two main parts:
1. Setting up communication with server.
    This part focuses on establishing and managing communication with server. 
    I use socket library here to handle connection and wrap the socket with TSL using ssl library. 
    To handle message, the received massages here are parsed into dictionaries. 
2. Handling the Guessing Process.
    In this part, the core is to filter the wordlist to decrease the times of guessing words.
    I use function here to check if the word in the current wordlist match the guess marks.
--------------------------------------------------
# Challenge
One of the main challenges I faced was running the client correctly. Before this project, I had not used a shebang in Python scripts.
Additionally, I was unfamiliar with certain aspects of TCP networking and socket. 
This led to issues where my client failed to connect to the server or could not run properly.
--------------------------------------------------
# Guessing Strategy
I implemented function to filter wordlist. After each guess, I use the marks from the server to filter wordlist. 
The wordlist is iterated, and each word is checked against the marks from the previous guess. If a word matches the criteria defined by the marks, it is added to the filtered wordlist.
And after each guess, the filtered wordlist is used iteratively to further reduce its length, ensuring that subsequent guesses are more targeted and efficient.