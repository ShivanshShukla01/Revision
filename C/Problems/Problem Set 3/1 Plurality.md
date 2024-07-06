### Question

Elections come in all shapes and sizes. In the UK, the Prime Minister is officially appointed by the monarch, who generally chooses the leader of the political party that wins the most seats in the House of Commons. The United States uses a multi-step Electoral College process where citizens vote on how each state should allocate Electors who then elect the President.

Perhaps the simplest way to hold an election, though, is via a method commonly known as the “plurality vote” (also known as “first-past-the-post” or “winner take all”). In the plurality vote, every voter gets to vote for one candidate. At the end of the election, whichever candidate has the greatest number of votes is declared the winner of the election.

### Assumed Output

```
./plurality Alice Bob Charlie
Number of voters: 3
Vote: Alice
Vote: Alice
Vote: Bob
Alice

./plurality Alice Bob Charlie
Number of voters: 3
Vote: David
Invalid Vote.
Vote: Alice
Vote: Alice
Alice

./plurality Alice Bob Charlie
Number of voters: 3
Vote: Alice
Vote: Bob
Vote: Charlie

./plurality
Usage: plurality [canditate ...]
```

### Solution

```c
#include <cs50.h>
#include <stdio.h>
#include <string.h>

// Max number of candidates
#define MAX 9

// Candidates have name and vote count
typedef struct
{
    string name;
    int votes;
} candidate;

// Array of candidates
candidate candidates[MAX];

// Number of candidates
int candidate_count;

// Function prototypes
int vote(string name);
void print_winner(void);

int main(int argc, string argv[])
{
    // Check for invalid usage
    if (argc < 2)
    {
        printf("Usage: plurality [candidate ...]\n");
        return 1;
    }

    // Populate array of candidates
    candidate_count = argc - 1;
    if (candidate_count > MAX)
    {
        printf("Maximum number of candidates is %i\n", MAX);
        return 2;
    }
    for (int i = 0; i < candidate_count; i++)
    {
        candidates[i].name = argv[i + 1];
        candidates[i].votes = 0;
    }

    int voter_count = get_int("Number of voters: ");

    // Loop over all voters
    for (int i = 0; i < voter_count; i++)
    {
        string name = get_string("Vote: ");

        // Check for invalid vote
        if (vote(name) != 1)
        {
            printf("Invalid vote.\n");
        }
    }

    // Display winner of election
    print_winner();
}

// Update vote totals given a new vote
int vote(string name)
{
    int verification_count = 0;
    for (int i = 0; i < candidate_count; i++)
    {
        if (strcmp(name, candidates[i].name) == 0)
        {
            candidates[i].votes++;
            verification_count++;
        }
    }
    return verification_count;
}

// Print the winner (or winners) of the election
void print_winner(void)
{
    int winner_votes = 0;
    for (int i = 0; i < candidate_count; i++)
    {
        if (candidates[i].votes > winner_votes)
        {
            winner_votes = candidates[i].votes;
        }
    }
    for (int i = 0; i < candidate_count; i++)
    {
        if (candidates[i].votes == winner_votes)
        {
            printf("%s\n", candidates[i].name);
        }
    }
    return;
}

```

> [!NOTE]
> How this works ?

- The define tag is something new here - the definition below is told by Meta AI
  - In C programming, `#define` is a preprocessor directive used to define a macro, which is a sequence of tokens that can be expanded to a larger code fragment.
  - Think of `#define` like a shortcut or an alias.
  - You can use `#define` to give a name to a piece of code or a value, so you can use that name instead of writing the whole code or value every time.
  - <u>For example</u>, instead of writing 3.14 every time you need the value of pi, you can use `#define` to give it a shortcut name like PI. Then, you can just use PI in your code, and it will be replaced with 3.14.
  - It's like creating a nickname for a piece of code or a value, so you can use the nickname instead of the original code or value.
- The Working is like
- Defined a variable MAX
- Created a Datatype using typedef struct
- Created an Array of size MAX
- Created the `main` function in the way they take input from the command line
- Making sure the Candidates must be entered and there are not more than 9 candidates
- Putting all the provided candidates name in the array created earlier.
- Asking number of voters and taking input respective number of times
- In the `vote` function
  - comparing the name entered by the voter and if that name is present, increasing that persons vote
  - If all the votes are invalid, declaring the same
- In the `print_winner` function
  - calculating the highest number of votes
  - and displaying the person receives it
