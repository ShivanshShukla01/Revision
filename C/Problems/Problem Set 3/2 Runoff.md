### Question

You already know about plurality elections, which follow a very simple algorithm for determining the winner of an election: every voter gets one vote, and the candidate with the most votes wins.

But the plurality vote does have some disadvantages. What happens, for instance, in an election with three candidates, and the ballots below are cast?
![](https://i.imgur.com/HEyBxtx.png)
A plurality vote would here declare a tie between Alice and Bob, since each has two votes. But is that the right outcome?

There’s another kind of voting system known as a ranked-choice voting system. In a ranked-choice system, voters can vote for more than one candidate. Instead of just voting for their top choice, they can rank the candidates in order of preference. The resulting ballots might therefore look like the below.
![](https://i.imgur.com/rzNfnGb.png)
Here, each voter, in addition to specifying their first preference candidate, has also indicated their second and third choices. And now, what was previously a tied election could now have a winner. The race was originally tied between Alice and Bob, so Charlie was out of the running. But the voter who chose Charlie preferred Alice over Bob, so Alice could here be declared the winner.

Ranked choice voting can also solve yet another potential drawback of plurality voting. Take a look at the following ballots.
![](https://i.imgur.com/Nq8SVYJ.png)
Who should win this election? In a plurality vote where each voter chooses their first preference only, Charlie wins this election with four votes compared to only three for Bob and two for Alice. But a majority of the voters (5 out of the 9) would be happier with either Alice or Bob instead of Charlie. By considering ranked preferences, a voting system may be able to choose a winner that better reflects the preferences of the voters.

One such ranked choice voting system is the instant runoff system. In an instant runoff election, voters can rank as many candidates as they wish. If any candidate has a majority (more than 50%) of the first preference votes, that candidate is declared the winner of the election.

If no candidate has more than 50% of the vote, then an “instant runoff” occurrs. The candidate who received the fewest number of votes is eliminated from the election, and anyone who originally chose that candidate as their first preference now has their second preference considered. Why do it this way? Effectively, this simulates what would have happened if the least popular candidate had not been in the election to begin with.

The process repeats: if no candidate has a majority of the votes, the last place candidate is eliminated, and anyone who voted for them will instead vote for their next preference (who hasn’t themselves already been eliminated). Once a candidate has a majority, that candidate is declared the winner.

Sounds a bit more complicated than a plurality vote, doesn’t it? But it arguably has the benefit of being an election system where the winner of the election more accurately represents the preferences of the voters.

### Assumed Output

```
$ ./runoff Alice Bob Charlie
Number of voters: 3
Rank 1: Alice
Rank 2: Bob
Rank 3: Charlie

Rank 1: Bob
Rank 2: Alice
Rank 3: Charlie

Rank 1: Alice
Rank 2: Bob
Rank 3: Charlie

Alice

$ ./runoff Alice Bob Charlie
Number of voters: 3
Rank 1: David
Invalid Vote.

$ ./runoff
Usage: runoff [candidate ...]
```

### Solution

```c
#include <cs50.h>
#include <stdio.h>
#include <string.h>

// Max voters and candidates
#define MAX_VOTERS 100
#define MAX_CANDIDATES 9

// preferences[i][j] is jth preference for voter i
int preferences[MAX_VOTERS][MAX_CANDIDATES];

// Candidates have name, vote count, eliminated status
typedef struct
{
    string name;
    int votes;
    bool eliminated;
} candidate;

// Array of candidates
candidate candidates[MAX_CANDIDATES];

// Numbers of voters and candidates
int voter_count;
int candidate_count;
int rank_count = 0;

// Function prototypes
bool vote(int voter, int rank, string name);
void tabulate(void);
bool print_winner(void);
int find_min(void);
bool is_tie(int min);
void eliminate(int min);

int main(int argc, string argv[])
{
    // Check for invalid usage
    if (argc < 2)
    {
        printf("Usage: runoff [candidate ...]\n");
        return 1;
    }

    // Populate array of candidates
    candidate_count = argc - 1;
    if (candidate_count > MAX_CANDIDATES)
    {
        printf("Maximum number of candidates is %i\n", MAX_CANDIDATES);
        return 2;
    }
    for (int i = 0; i < candidate_count; i++)
    {
        candidates[i].name = argv[i + 1];
        candidates[i].votes = 0;
        candidates[i].eliminated = false;
    }

    voter_count = get_int("Number of voters: ");
    if (voter_count > MAX_VOTERS)
    {
        printf("Maximum number of voters is %i\n", MAX_VOTERS);
        return 3;
    }

    // Keep querying for votes
    for (int i = 0; i < voter_count; i++)
    {

        // Query for each rank
        for (int j = 0; j < candidate_count; j++)
        {
            string name = get_string("Rank %i: ", j + 1);

            // Record vote, unless it's invalid
            if (!vote(i, j, name))
            {
                printf("Invalid vote.\n");
                return 4;
            }
        }
        printf("\n");
    }

    // Keep holding runoffs until winner exists
    while (true)
    {
        // Calculate votes given remaining candidates
        tabulate();

        // Check if election has been won
        bool won = print_winner();
        if (won)
        {
            break;
        }

        // Eliminate last-place candidates
        int min = find_min();
        bool tie = is_tie(min);

        // If tie, everyone wins
        if (tie)
        {
            for (int i = 0; i < candidate_count; i++)
            {
                if (!candidates[i].eliminated)
                {
                    printf("%s\n", candidates[i].name);
                }
            }
            break;
        }

        // Eliminate anyone with minimum number of votes
        eliminate(min);

        // Reset vote counts back to zero
        for (int i = 0; i < candidate_count; i++)
        {
            candidates[i].votes = 0;
        }
    }

    return 0;
}

// Record preference if vote is valid
bool vote(int voter, int rank, string name)
{
    //to verify whether the candidate is in the given cadidate list or not
    int verification_counter = 0;

    // checking that if name is in the list of candidates or not
    //if yes, put in the preference table which is a 2d array
    for (int i = 0; i < candidate_count; i++)
    {
        if (strcmp(name, candidates[i].name) == 0)
        {
            //voter's rank is collecting the index of the candidate name in the 2D-Array as per there index in the candidates array
            preferences[voter][rank] = i;
            verification_counter++;
        }
    }
    if (verification_counter != 1)
    {
        return false;
    }
    else
    {
        return true;
    }

}

// Tabulate votes for non-eliminated candidates
void tabulate(void)
{
    //checking if the candidate is eliminated or not, if not, matching the index of the candidate with the preference table and if found, increase the votes of the respective index candidate
    //iterating through each voter
    for (int i = 0; i < voter_count; i++)
    {
        //iterating thorugh each candidate
        for (int j = 0; j < candidate_count; j++)
        {
            //storing the index of candidate in a variable
            int candidate_index = preferences[i][j];

            //if candidate is not eliminated
            if (!candidates[candidate_index].eliminated)
            {
                //increase the vote of candidate
                candidates[candidate_index].votes++;
                break;
            }
        }
    }

    return;
}


// Print the winner of the election, if there is one
bool print_winner(void)
{
    // if the votes are majority, means more than 50% of number of voters, then win
    for (int i = 0; i < candidate_count; i++)
    {
        //if votes of candidate is majority. the he/she wins
        if (candidates[i].votes > voter_count / 2)
        {
            printf("%s\n", candidates[i].name);
            return true;
        }
    }
    return false;
}

// Return the minimum number of votes any remaining candidate has
int find_min(void)
{
    //initializing a variable to calculate minimum number of votes
    int minimum_votes;
    for (int  i = 0; i < candidate_count; i++)
    {
        //if first candidate who is not eliminated
        if (!candidates[i].eliminated)
        {
            //his votes become the minimum votes
            minimum_votes = candidates[i].votes;
        }
    }
    //iterating through each candidate skipping the 1st one who is not eliminated as his votes are already stores in the minimum votes
    for (int i = 1; i < candidate_count; i++)
    {
        //if candidate is not eliminated and his votes are less than minimum votes
        if (!candidates[i].eliminated && candidates[i].votes < minimum_votes)
        {
            //then his votes will be minimum votes
            minimum_votes = candidates[i].votes;
        }
    }
    return minimum_votes;
}

// Return true if the election is tied between all candidates, false otherwise
bool is_tie(int min)
{
    //if everyone who is not eliminated has same number of votes, then it is a tie
    //intializing variables for tasks
    int compare_var;
    int cycle_count = 0;
    int confirm_count = 0;
    //iterating through each candidate
    for (int i = 0; i < candidate_count; i++)
    {
        //the votes of the first non-eliminated candidate
        if (!candidates[i].eliminated)
        {
            //will be stored in the compare var
            compare_var = candidates[i].votes;
            //and break out of this loop
            break;
        }
    }
    //iterating through each candidate
    for (int i = 0; i < candidate_count; i++)
    {
        //if candidate is eliminated
        if (!candidates[i].eliminated)
        {
            //increase the cycle count
            cycle_count++;
            //if the votes of candidate are equal to compare var
            if (candidates[i].votes == compare_var)
            {
                //increase the confirm count
                confirm_count++;
            }
        }
    }
    //if confirm count = cycle count, it means the votes of all the non-eliminated candidates if same
    if (confirm_count == cycle_count)
    {
        return true;
    }
    else
    {
        return false;
    }

}

// Eliminate the candidate (or candidates) in last place
void eliminate(int min)
{
    //the candidate having minimum vote is eliminated
    for (int i = 0; i < candidate_count; i++)
    {
        //if the votes of non-eliminated candidate is equal to minimum votes
        if (candidates[i].votes == min)
        {
            //the candidate is going to be eliminated
            candidates[i].eliminated = true;
        }
    }
    return;
}

```

> [!NOTE]
> How this works ?

- This is a fantastic program to revise things
- Explanation of the function is available in the comments
- Creations
  - Max number of voters and candidates are defined
  - 2D - Array is created with the values defined above
  - Custom datatype - Candidate to store name, votes and their elimination status
  - Candidates array to store the candidates entered by the user
  - some variables are initialised
- Main functions verify there must be two commands given and then number of candidates must be between 1 - 9
- then a loop is used to enter the details of the candidates in the candidates array
- ask the user for the number of voters
- next loop asks every voter to rank the candidates as per their preference and the validity of the vote is examined by `vote` function
  - if their is any vote which is invalid, then the program will end
- `tabulate` function calculate the votes of each non eliminated candidate as per the preference of the voters
- if there is any winner in the first round which can be, if he/she have majority votes means more than the number half of the number of voters, `print_winner` function will provide the name
- if there is no winner, the `find_min` function find the candidate(s) with minimum number of vote
- if every candidate has minimum or say same amount of vote, `is_tie` function take care of it
- if there is no tie, `eliminate` function will eliminate the candidate(s) having least number of votes
- AND cycle will repeat from the `tabulate` function until there is a winner or there is tie between the candidates
