# Question

You already know about plurality elections, which follow a very simple algorithm for determining the winner of an election: every voter gets one vote, and the candidate with the most votes wins.

But the plurality vote does have some disadvantages. What happens, for instance, in an election with three candidates, and the ballots below are cast?

![](https://i.imgur.com/HEyBxtx.png)

A plurality vote would here declare a tie between Alice and Bob, since each has two votes. But is that the right outcome?

There’s another kind of voting system known as a ranked-choice voting system. In a ranked-choice system, voters can vote for more than one candidate. Instead of just voting for their top choice, they can rank the candidates in order of preference. The resulting ballots might therefore look like the below.

![](https://i.imgur.com/rzNfnGb.png)

Here, each voter, in addition to specifying their first preference candidate, has also indicated their second and third choices. And now, what was previously a tied election could now have a winner. The race was originally tied between Alice and Bob, so Charlie was out of the running. But the voter who chose Charlie preferred Alice over Bob, so Alice could here be declared the winner.

Ranked choice voting can also solve yet another potential drawback of plurality voting. Take a look at the following ballots.

![](https://i.imgur.com/Nq8SVYJ.png)

Who should win this election? In a plurality vote where each voter chooses their first preference only, Charlie wins this election with four votes compared to only three for Bob and two for Alice. (Note that, if you’re familiar with the instant runoff voting system, Charlie wins here under that system as well). Alice, however, might reasonably make the argument that she should be the winner of the election instead of Charlie: after all, of the nine voters, a majority (five of them) preferred Alice over Charlie, so most people would be happier with Alice as the winner instead of Charlie.

Alice is, in this election, the so-called “Condorcet winner” of the election: the person who would have won any head-to-head matchup against another candidate. If the election had been just Alice and Bob, or just Alice and Charlie, Alice would have won.

The Tideman voting method (also known as “ranked pairs”) is a ranked-choice voting method that’s guaranteed to produce the Condorcet winner of the election if one exists.

# Assumed Output

```
$ ./tideman Alice Bob Charlie
Number of voters: 3
Rank 1: Alice
Rank 2: Bob
Rank 3: Charlie

Rank 1: Bob
Rank 2: Charlie
Rank 3: Alice

Rank 1: Alice
Rank 2: Bob
Rank 3: Charlie

Alice

$ ./tideman Alice Bob Charlie
Number of voters: 3
Rank 1: David
Invalid Vote.

$ ./tideman
Usage: tideman [candidate ...]
```

# Background

Generally speaking, the Tideman method works by constructing a “graph” of candidates, where an arrow (i.e. edge) from candidate A to candidate B indicates that candidate A wins against candidate B in a head-to-head matchup. The graph for the above election, then, would look like the below.

![](https://i.imgur.com/qleiaap.png)

The arrow from Alice to Bob means that more voters prefer Alice to Bob (5 prefer Alice, 4 prefer Bob). Likewise, the other arrows mean that more voters prefer Alice to Charlie, and more voters prefer Charlie to Bob.

Looking at this graph, the Tideman method says the winner of the election should be the “source” of the graph (i.e. the candidate that has no arrow pointing at them). In this case, the source is Alice — Alice is the only one who has no arrow pointing at her, which means nobody is preferred head-to-head over Alice. Alice is thus declared the winner of the election.

It’s possible, however, that when the arrows are drawn, there is no Condorcet winner. Consider the below ballots.

![](https://i.imgur.com/0CJEPvC.png)

Between Alice and Bob, Alice is preferred over Bob by a 7-2 margin. Between Bob and Charlie, Bob is preferred over Charlie by a 5-4 margin. But between Charlie and Alice, Charlie is preferred over Alice by a 6-3 margin. If we draw out the graph, there is no source! We have a cycle of candidates, where Alice beats Bob who beats Charlie who beats Alice (much like a game of rock-paper-scissors). In this case, it looks like there’s no way to pick a winner.

To handle this, the Tideman algorithm must be careful to avoid creating cycles in the candidate graph. How does it do this? The algorithm locks in the strongest edges first, since those are arguably the most significant. In particular, the Tideman algorithm specifies that matchup edges should be “locked in” to the graph one at a time, based on the “strength” of the victory (the more people who prefer a candidate over their opponent, the stronger the victory). So long as the edge can be locked into the graph without creating a cycle, the edge is added; otherwise, the edge is ignored.

How would this work in the case of the votes above? Well, the biggest margin of victory for a pair is Alice beating Bob, since 7 voters prefer Alice over Bob (no other head-to-head matchup has a winner preferred by more than 7 voters). So the Alice-Bob arrow is locked into the graph first. The next biggest margin of victory is Charlie’s 6-3 victory over Alice, so that arrow is locked in next.

Next up is Bob’s 5-4 victory over Charlie. But notice: if we were to add an arrow from Bob to Charlie now, we would create a cycle! Since the graph can’t allow cycles, we should skip this edge, and not add it to the graph at all. If there were more arrows to consider, we would look to those next, but that was the last arrow, so the graph is complete.

This step-by-step process is shown below, with the final graph at right.

![](https://i.imgur.com/oFqA3tE.png)

Based on the resulting graph, Charlie is the source (there’s no arrow pointing towards Charlie), so Charlie is declared the winner of this election.

Put more formally, the Tideman voting method consists of three parts:

- **Tally**: Once all of the voters have indicated all of their preferences, determine, for each pair of candidates, who the preferred candidate is and by what margin they are preferred.
- **Sort**: Sort the pairs of candidates in decreasing order of strength of victory, where strength of victory is defined to be the number of voters who prefer the preferred candidate.
- **Lock**: Starting with the strongest pair, go through the pairs of candidates in order and “lock in” each pair to the candidate graph, so long as locking in that pair does not create a cycle in the graph.

Once the graph is complete, the source of the graph (the one with no edges pointing towards it) is the winner!

# Understanding

Let’s take a look at `tideman.c`.

First, notice the two-dimensional array `preferences`. The integer `preferences[i][j]` will represent the number of voters who prefer candidate `i` over candidate `j`.

The file also defines another two-dimensional array, called `locked`, which will represent the candidate graph. `locked` is a boolean array, so `locked[i][j]` being `true` represents the existence of an edge pointing from candidate `i` to candidate `j`; `false` means there is no edge. (If curious, this representation of a graph is known as an “adjacency matrix”).

Next up is a `struct` called `pair`, used to represent a pair of candidates: each pair includes the `winner`’s candidate index and the `loser`’s candidate index.

The candidates themselves are stored in the array `candidates`, which is an array of `string`s representing the names of each of the candidates. There’s also an array of `pairs`, which will represent all of the pairs of candidates (for which one is preferred over the other) in the election.

The program also has two global variables: `pair_count` and `candidate_count`, representing the number of pairs and number of candidates in the arrays `pairs` and `candidates`, respectively.

Now onto `main`. Notice that after determining the number of candidates, the program loops through the `locked` graph and initially sets all of the values to `false`, which means our initial graph will have no edges in it.

Next, the program loops over all of the voters and collects their preferences in an array called `ranks` (via a call to `vote`), where `ranks[i]` is the index of the candidate who is the `i`th preference for the voter. These ranks are passed into the `record_preference` function, whose job it is to take those ranks and update the global `preferences` variable.

Once all of the votes are in, the pairs of candidates are added to the `pairs` array via a called to `add_pairs`, sorted via a call to `sort_pairs`, and locked into the graph via a call to `lock_pairs`. Finally, `print_winner` is called to print out the name of the election’s winner!

Further down in the file, you’ll see that the functions `vote`, `record_preference`, `add_pairs`,`sort_pairs`, `lock_pairs`, and `print_winner` are left blank. That’s up to you!

# Solution

### Approach 1 -> The edge of the graph will be drawn - check if the created line created an infinite cycle - if yes, erase that edge

```c
/*

YE WALA FIRST TIME JO LIKHA THA, WO HAI ISME SABSE ZYADA COMMENTING HAS AS EXPLAINING EVERY SINGLE THINGS BUT ISKE ALAWA WALE VERSION MEIN KEVAL CHANGES KO HI COMMENTS KE THROUGH EXPLAIN KARA HAI

Approches:

1) graph ki line draw karo and agar usko draw karne se infinte cycle ban rahi hai to usko waapis erase kar do

2) agar graph ki line banane se graph mein infinte cycle ban rahi hai to graph ko draw hi mat karo which sounds like ki jo problem ki requirement hai, us pair ko skip karne ki jiski wajah se infinite loop ban raha to yes sahi hai but pata nhi kya problem hai

3) is approach mein ek doosri table banaye hai same to same locked jaise and usme lines ko draw kiya hai and jin jin lines ko draw ko karne par infinte cycle nhi ban rahi, unko ek array mein save kar liya hai and phir un arrays se locked table ko banaya hai jo ki yeah, sound like skip kiya hai but ye bhi consider nhi kara - iska code yahan nhi pada hai kyonki second apporach ke similar hi tha to add karna jaroori nhi samjha - 3RD APPROACH MEIN BASICALLY 2ND WALE HI APPROACH LAAGU KARE THE BUT SAB KUCH ALAG ALAG KARNE SO BASICALLY KAHA JAYE TO TIME WASTE KARE THE KYONKI WAHI SAME CHEEZ JO USME HUII HAI WO ISME HUII THI

4) isme chatGPT ka code hai and chatGPT ke hi comments hai, maine shayad kiya hai ya shayad nhi kiya hai

JITNE CODE KHUD SE LIKHE HAI SABME EXPLANATION COMMENTS MEIN HAI KEVAL GPT WALE KI LIYE HOW THIS WORKS ? MEIN INFORMATION PROVIDE KARI HAI




YE DEGUBUGGING AND UNDERSTANDING MEIN BHI HELP KARTA HAI AS ISME SAB CHEEZEIN MEANS PREFERNCE TABEL AND LOCKED TABLE BHI PRINT HOKE AATI HAI

*/
//importing the libraries and cs50 for get_int() and get_string()
#include <cs50.h>
#include <stdio.h>
#include <string.h>

// Max number of candidates
#define MAX 9

// preferences[i][j] is number of voters who prefer i over j
//isi table mein store honge preferences of one over another, ki i ko j se zyada kitne log prefer kar rahe hai
int preferences[MAX][MAX];

// locked[i][j] means i is locked in over j
//ye responsible hai for making the graph, i locked in over j ka matlab hai ki i se j ki taraf arrow banaya hai, agar i and j ke beech mukabala ho to i winner hoga election and graph ke hisaab se baat kari jayee to i se start hoga graph j ki taraf and point karega on j - source is i and arrow is pointing towards j
bool locked[MAX][MAX];

// Each pair has a winner, loser
typedef struct
{
    int winner;
    int loser;
} pair;

// Array of candidates
string candidates[MAX];
pair pairs[MAX * (MAX - 1) / 2];

int pair_count;
int candidate_count;

// Function prototypes
bool vote(int rank, string name, int ranks[]);
void record_preferences(int ranks[]);
void add_pairs(void);
void sort_pairs(void);
void lock_pairs(void);
void print_winner(void);
void no_print(int affected_index);
void bubble_sort(int array[]);

int main(int argc, string argv[])
{
    // Check for invalid usage
    if (argc < 2)
    {
        printf("Usage: tideman [candidate ...]\n");
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
        candidates[i] = argv[i + 1];
    }

    //preference mein saari value ko 0 kiya as ki abhi koi bhi candidate kisi ke upar bhi preferred nhi hai
    for (int i = 0; i < candidate_count; i++)
    {
        for (int j = 0; j < candidate_count; j++)
        {
            preferences[i][j] = 0;
        }
    }

    // Clear graph of locked in pairs
    //and abhi graph bhi nhi bana hai to yeah wahi yahan par bhi kiya hai
    for (int i = 0; i < candidate_count; i++)
    {
        for (int j = 0; j < candidate_count; j++)
        {
            locked[i][j] = false;
        }
    }

    //kitne pairs honge pair datatype ke pairs mein
    pair_count = 0;
    int voter_count = get_int("Number of voters: ");

    // Query for votes
    for (int i = 0; i < voter_count; i++)
    {
        // ranks[i] is voter's ith preference
        printf("Voter %i----\n", i + 1);
        int ranks[candidate_count];

        // Query for each rank
        for (int j = 0; j < candidate_count; j++)
        {
            string name = get_string("Rank %i: ", j + 1);

            if (!vote(j, name, ranks))
            {
                printf("Invalid vote.\n");
                return 3;
            }
        }
        record_preferences(ranks);

        printf("\n");
    }

    printf("\n");

    printf("----PREFERENCES CHART----");

    printf("\n");

    for (int p = 0; p < candidate_count; p++)
    {
        for (int q = 0; q < candidate_count; q++)
        {
            printf("%i over %i is %i\n", p, q, preferences[p][q]);
        }
        printf("\n");
    }

    printf("\n");
    printf("----ADDED PAIRS----");
    printf("\n");

    add_pairs();

    for (int i = 0; i < pair_count; i++)
    {
        printf("%i,%i\n", pairs[i].winner, pairs[i].loser);
    }

    printf("\n");

    sort_pairs();

    printf("----SORTED PAIRS----");
    printf("\n");


    for (int i = 0; i < pair_count; i++)
    {
        printf("%i,%i\n", pairs[i].winner, pairs[i].loser);
    }

    printf("\n");

    lock_pairs();

    printf("----Boolean Table----");

    printf("\n");

    for (int i = 0; i < candidate_count; i++)
    {
        for (int j = 0; j < candidate_count; j++)
        {
            printf("%i over %i is %s\n", i, j, locked[i][j] ? "true" : "false");
        }
        printf("\n");
    }

    print_winner();
    return 0;
}

// Update ranks given a new vote
bool vote(int rank, string name, int ranks[])
{
    //a variable is initiated to make sure the voting is done correctly and return true
    int confirm_var = 0;
    //iterate over the candidates
    for (int i = 0; i < candidate_count; i++)
    {
        //compare the input by user and names in the candidate array
        if (strcmp(name, candidates[i]) == 0)
        {
            //when matched, put the index of the matched candidate in the ranks array. Index in candidates array ki baat ho rahi hai
            ranks[rank] = i;
            confirm_var++;
        }
    }

    if (confirm_var == 1)
    {
        return true;
    }
    else
    {
        return false;
    }
}

// Update preferences given one voter's ranks
void record_preferences(int ranks[])
{
    //ranks array mein jo jo content pada hua hai , usko uske baad wale se compare karne ke liye ye loop hai
    //loop (candidate_count - 1) is liye hai because jitne candidate honge usse ek value kam ka max index hoga and wo last position hai hence usse compare karne ke liye uske aage koi hoga hence wo last prefered rahega
    for (int i = 0; i < (candidate_count - 1); i++)
    {
        //ye loop, jis bhi candidate ke index ki position se compare kar rahe hai uske aage aane wale candidates se compare karne ke liye hai isliye 1 se start huaa hai
        for (int j = 1; j < candidate_count; j++)
        {
            //conditional isliye laga kyonki (i + j) ka use hoga un candidates ko access karne ke liye jisse i jis position ko hold karega uske baad wale se compare karne ke liye
            // (i + j) se isliye ki wo khud i se chota na ho jaye kyonki huko (agar hum array ko horizontal maane to) right side ki taraf hi move karenge comparison karne ke liye and less than candidate count isliye ki isse wo hamesa last wale tak hi jayega compare karne ke liye
            if ((i + j) < candidate_count)
            {
                //variable mein ranks[] mein wo variable jisko prefer kiya hai and jin jin ke upar uske hisaab se count ho jayegaa kyonki jitne se upar hoga, unki value plus hogi uske hisaab se
                //starting mein code to waise bhi preferences[][] 2D array ki saari values ko zero hi kar diya hai
                int prefered = ranks[i];
                int over = ranks[i + j];
                preferences[prefered][over]++;
                //in case naa samaj aa raha ho ki upar wala code kaise kaam kar raha hai to ek chart banakar code ko follow karte jaoo and samaj jaoge ki kis tareeke se values update ho rahi hai
            }
        }
    }
    return;
}

// Record pairs of candidates where one is preferred over the other
void add_pairs(void)
{

    /*

    ISME COMMENTATION MAINE BAAD MEIN LIKHA HAI AND ISKO AB WAPIS SE SAMAJ KE LIKHNE MEIN PHOKAT KA TIME WASTE HOGA TO TUM ABHI REVISION KAR RAHE HO TO ISKO LINE BY LINE WHITEBOARD AAP PE RUN KARKE DEKHO LO AND ISKI WORKING SAMAJ LO AND MAN HO TO USI SAMAY ISME COMMENTATION BHI KARDENA WAISE YE TAREEKA KAAFI ZYADA INEFFICIANT HAI BUT JIS SAMAY YE LIKHA US SAMAY ITNI BUDDHI THI HI NHI TUMHARE PAAS TO KOI BAAT NHI

    */
    //iterate through each candidate
    for (int i = 0; i < (candidate_count - 1); i++)
    {
        for (int j = 1; j < candidate_count; j++)
        {

            if ((preferences[i][j] > preferences[j][i]) && j > i )
            {
                if (pair_count == 0)
                {
                    pairs[pair_count].winner = i;
                    pairs[pair_count].loser = j;
                    pair_count++;

                }

                else
                {
                    for(int k = 0; k < pair_count; k++)
                    {
                        if ((pairs[k].winner != i && pairs[k].loser != j) || (pairs[k].winner != j && pairs[k].loser != i))
                        {
                            pairs[pair_count].winner = i;
                            pairs[pair_count].loser = j;
                            pair_count++;
                            break;
                        }
                    }
                }
            }

            else if ((preferences[i][j] < preferences[j][i]) && j > i)
            {
                if (pair_count == 0)
                {
                    pairs[pair_count].winner = j;
                    pairs[pair_count].loser = i;
                    pair_count++;

                }

                else
                {

                    for(int k = 0; k < pair_count; k++)
                    {
                        if ((pairs[k].winner != j && pairs[k].loser != i) || (pairs[k].winner != i && pairs[k].loser != j))
                        {
                            pairs[pair_count].winner = j;
                            pairs[pair_count].loser = i;
                            pair_count++;
                            break;
                        }
                    }
                }
            }
        }
    }


    return;
}

// Sort pairs in decreasing order by strength of victory
void sort_pairs(void)
{
    //ek temporary arry jiske upar saari sorting hogi kyonki original mein sorting karne par data mein change ho jaa raha tha halaki isme sorting karne ke baad iske hi basis par original mein changes kiya hai but aise kyon kiya wo to nhi yaad but haa ye first approach thi
    int temp_array[pair_count];

    //temp array mein saare pairs ko daal rahe hai
    for (int i = 0; i < pair_count; i++)
    {
        temp_array[i] = preferences[pairs[i].winner][pairs[i].loser];
    }

    printf("\n");
    printf("----ADDED PAIRS VALUES----");
    printf("\n");

    for (int i = 0; i < pair_count; i++)
    {
        printf("%i\n", temp_array[i]);
    }

    bubble_sort(temp_array); //bubble sorting isliye lagaya hai kyonki us samaye tak merge sort ka nhi bana paye the and selection sort ka baad mein bana paye the lekin jab dono ka lumsum barabar hi hai big-O notation to change nhi kiya and change karne mein phir waise bhi time lagta

    printf("\n");
    printf("----ADDED PAIRS VALUES(SORTED)----");
    printf("\n");

    for (int i = 0; i < pair_count; i++)
    {
        printf("%i\n", temp_array[i]);
    }

    //phir jaise pairs bana hai original,waise hai same size ka keval naam change karke banaya hai
    pair sorted_pairs[MAX * (MAX - 1) / 2];

    //to access the pairs array
    for (int i = 0; i < pair_count; i++)
    {
        //storing the integer value of the pairs array
        int compare_num = preferences[pairs[i].winner][pairs[i].loser];
        //loop to access temp_array
        for (int j = 0; j < pair_count; j++)
        {
            if (temp_array[j] == compare_num)
            {
                //yahan par sorted pairs mein pairs ko daal hai
                sorted_pairs[j].winner = pairs[i].winner;
                sorted_pairs[j].loser = pairs[i].loser;
                //a value that is for sure not going to match
                temp_array[j] = 0;
                break;
            }
        }
    }

    //phir upar banaye gayee sorted pairs se original wale pairs mein pair ki arrays ko daal diya hai, yaha par kai saari cheezein extra kari gayii hai but us samay yahi samaj mein aaya
    for (int i = 0; i < pair_count; i++)
    {
        pairs[i].winner = sorted_pairs[i].winner;
        pairs[i].loser = sorted_pairs[i].loser;
    }


    return;
}

//ab isko batane ki zaroorat nhi hai iska alag se ek banakar rakha hai pahele se jaake waha dekh lo
void bubble_sort(int array[])
{
    int swap_count = 0;
    for (int i = 0; i < (pair_count - 1); i++)
    {
        if (array[i] < array[i + 1])
        {
            int swap_var = array[i];
            array[i] = array[i + 1];
            array[i + 1] = swap_var;
            swap_count++;
        }
    }

    if (swap_count == 0)
    {
        return;
    }

    //isme recursion lagta hai
    bubble_sort(array);
}


// Lock pairs into the candidate graph in order, without creating cycles
void lock_pairs(void)
{
    //ab jaise ki ye code pahele graph ki line draw karega and phir check karega ki kya iski wajah se infinite cycle ban gyii hai kya, agar haa to usko erase kar denga and nhi bani hai to usko rahen dega, isme utni zyada issues to nhi the
    //actually kisi bhi self ke code mein major issues nhi the, same cheez karni but teeno mein approach lagaya hai alag alag to uska utna kuch faida nhi huaa
    //requirements thi ki pairs ko skip kardein agar wo cycle bana raha hai to and wo skip nhi ho raha tha, thore alag alag apporaches ka use kiya but wo requirement mein fit nhi baithe to phir 4-5 din mehnet karne ke baad last mein GPT ka sahara lena pada
    for (int i = 0; i < pair_count; i++)
    {
        locked[pairs[i].winner][pairs[i].loser] = true;
        //wo function jo check karna hai ki infininte cycle to nhi ban rahi and ban rahi hoti hai upar wali se jo line banti hai graph mein usko delete kar deta hai
        no_print(i);
    }
    return;
}

// Print the winner of the election
void print_winner(void)
{
    /*

    mere hisaab se mere code mein table is tareeke se bani thi locked wali

    suppose there were 4 candidates

        0    1    2    3

    0   F   (T)   F    F

    1   F    F   (T)   T

    2  (T)   F    F   (T)

    3   F    F    F    F

    ab is table mein column hai vertical and row and horizontal jo ki yahi hota hai
    column head represent the prefered candidate and row head represent prefered over candidate
    true ka matlab prefered se prefered over ki or ek arrow draw hai

    ab isme se jis bhi row mein ek bhi true nhi hai, wo hi winner hai kyonki true nhi hai uski row mein maltab koi bhi candidate uske upar prefer nhi kiya gya hai and agar kiya bhi gya hoga and agar wo infinite cycle bana raha ho, to usko graph ki line ko erase kar diya gya tha hence uski taraf koi bhi arrow point nhi kar raha which means ki wo winner hai jo source hai

    */

    for (int i = 0; i < candidate_count; i++)
    {
        int verify = 0;
        for (int j = 0; j < candidate_count; j++)
        {
            if (locked[j][i] == true)
            {
                 verify++;
                 break;
            }
        }
        if (verify == 0)
        {
            printf("%s\n", candidates[i]);
        }
    }
    return;
}

//ye simply aise kaam kar raha ki upar wale function mein jo table banaye hai, wo table ki har ek row mein jaa raha hai agar koi true mil raha to verify variable ko bada de raha matlab 2 mein mila to 2 row mein true hai, ab agar 4 candidate hai and 4 row mein true mil gya matlab ki last drawn graph ne ek cycle bana diya hence usko erase kar do
void no_print(int affected_index)
{
    int verify = 0;
    for (int i = 0; i < candidate_count; i++)
    {
        for (int j = 0; j < candidate_count; j++)
        {
            if (locked[j][i] == true)
            {
                verify++;
                break;
            }
        }
    }
    if (verify == candidate_count)
    {
        locked[pairs[affected_index].winner][pairs[affected_index].loser] = false;
        return;
    }
}

```

### Approach 2 -> If the creation of edge leads to the creation of infinite cycle - the edge will not get created

```c
//in the first approach, the edge(line) of graph from dominative to submissive will be drawn, check for the infinite cycle, if there is, erase the line
//in second approach, if the creation of line is responsible for infinite cycle, not draw that line at all
//ISME UTNA DETAILS SE EK EK CHEEZ NHI EXPLAIN KIYA HAI, KEVAL JITNA NEED LAGA UTNA KIYA HAI
//YE LAGBHAG SIMILAR HAI FIRST WALE SE KEVAL KUCH LAST MEIN CHANGES HAI
#include <cs50.h>
#include <stdio.h>
#include <string.h>

// Max number of candidates
#define MAX 9

// preferences[i][j] is number of voters who prefer i over j
int preferences[MAX][MAX];

// locked[i][j] means i is locked in over j
bool locked[MAX][MAX];
//YE TEMP LOCKED BANAYA HAI KEVAL VERIFICATION KE LIYE AND KYON KAISE WO AAGE PATA CHALEGA
bool temp_locked[MAX][MAX];

// Each pair has a winner, loser
typedef struct
{
    int winner;
    int loser;
} pair;

// Array of candidates
string candidates[MAX];
pair pairs[MAX * (MAX - 1) / 2];

int pair_count;
int candidate_count;

// Function prototypes
bool vote(int rank, string name, int ranks[]);
void record_preferences(int ranks[]);
void add_pairs(void);
void sort_pairs(void);
void lock_pairs(void);
void print_winner(void);
bool no_winner(int affected_index);
void bubble_sort(int array[]);

int main(int argc, string argv[])
{
    // Check for invalid usage
    if (argc < 2)
    {
        printf("Usage: tideman [candidate ...]\n");
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
        candidates[i] = argv[i + 1];
    }

    for (int i = 0; i < candidate_count; i++)
    {
        for (int j = 0; j < candidate_count; j++)
        {
            preferences[i][j] = 0;
        }
    }

    // Clear graph of locked in pairs
    for (int i = 0; i < candidate_count; i++)
    {
        for (int j = 0; j < candidate_count; j++)
        {
            locked[i][j] = false;
        }
    }

    pair_count = 0;
    int voter_count = get_int("Number of voters: ");

    // Query for votes
    for (int i = 0; i < voter_count; i++)
    {
        // ranks[i] is voter's ith preference
        printf("Voter %i----\n", i + 1);
        int ranks[candidate_count];

        // Query for each rank
        for (int j = 0; j < candidate_count; j++)
        {
            string name = get_string("Rank %i: ", j + 1);

            if (!vote(j, name, ranks))
            {
                printf("Invalid vote.\n");
                return 3;
            }
        }
        record_preferences(ranks);
        printf("\n");
    }

    printf("\n");
    printf("----PREFERENCES CHART----");
    printf("\n");

    for (int p = 0; p < candidate_count; p++)
    {
        for (int q = 0; q < candidate_count; q++)
        {
            printf("%i over %i is %i\n", p, q, preferences[p][q]);
        }
        printf("\n");
    }

    printf("\n");
    printf("----ADDED PAIRS----");
    printf("\n");

    add_pairs();

    for (int i = 0; i < pair_count; i++)
    {
        printf("%i,%i\n", pairs[i].winner, pairs[i].loser);
    }

    printf("\n");

    sort_pairs();

    printf("\n");
    printf("----SORTED PAIRS----");
    printf("\n");

    for (int i = 0; i < pair_count; i++)
    {
        printf("%i,%i\n", pairs[i].winner, pairs[i].loser);
    }

    printf("\n");

    lock_pairs();

    printf("----Boolean Table----");
    printf("\n");
    for (int i = 0; i < candidate_count; i++)
    {
        for (int j = 0; j < candidate_count; j++)
        {
            printf("%i over %i is %s\n", i, j, locked[i][j] ? "true" : "false");
        }
        printf("\n");
    }
    print_winner();
    return 0;
}

// Update ranks given a new vote
bool vote(int rank, string name, int ranks[])
{
    //a variable is initiated to make sure the voting is done correctly and return true
    int confirm_var = 0;
    //iterate over the candidates
    for (int i = 0; i < candidate_count; i++)
    {
        //compare the input by user and names in the candidate array
        if (strcmp(name, candidates[i]) == 0)
        {
            //when matched, put the index of the matched candidate in the ranks array. Index in candidates array ki baat ho rahi hai
            ranks[rank] = i;
            confirm_var++;
        }
    }

    if (confirm_var == 1)
    {
        return true;
    }
    else
    {
        return false;
    }
}

// Update preferences given one voter's ranks
void record_preferences(int ranks[])
{
    //ranks array mein jo jo content pada hua hai , usko uske baad wale se compare karne ke liye ye loop hai
    //loop (candidate_count - 1) is liye hai because jitne candidate honge usse ek value kam ka max index hoga and wo last position hai hence usse compare karne ke liye uske aage koi hoga hence wo last prefered rahega
    for (int i = 0; i < (candidate_count - 1); i++)
    {
        //ye loop, jis bhi candidate ke index ki position se compare kar rahe hai uske aage aane wale candidates se compare karne ke liye hai isliye 1 se start huaa hai
        for (int j = 1; j < candidate_count; j++)
        {
            //conditional isliye laga kyonki (i + j) ka use hoga un candidates ko access karne ke liye jisse i jis position ko hold karega uske baad wale se compare karne ke liye
            // (i + j) se isliye ki wo khud i se chota na ho jaye kyonki huko (agar hum array ko horizontal maane to) right side ki taraf hi move karenge comparison karne ke liye and less than candidate count isliye ki isse wo hamesa last wale tak hi jayega compare karne ke liye
            if ((i + j) < candidate_count)
            {
                //variable mein ranks[] mein wo variable jisko prefer kiya hai and jin jin ke upar uske hisaab se count ho jayegaa kyonki jitne se upar hoga, unki value plus hogi uske hisaab se
                //starting mein code to waise bhi preferences[][] 2D array ki saari values ko zero hi kar diya hai
                int prefered = ranks[i];
                int over = ranks[i + j];
                preferences[prefered][over]++;
                //in case naa samaj aa raha ho ki upar wala code kaise kaam kar raha hai to ek chart banakar code ko follow karte jaoo and samaj jaoge ki kis tareeke se values update ho rahi hai
            }
        }
    }
    return;
}

// Record pairs of candidates where one is preferred over the other
void add_pairs(void)
{
    //
    for (int i = 0; i < (candidate_count - 1); i++)
    {
        for (int j = 1; j < candidate_count; j++)
        {

            if ((preferences[i][j] > preferences[j][i]) && j > i )
            {
                if (pair_count == 0)
                {
                    pairs[pair_count].winner = i;
                    pairs[pair_count].loser = j;
                    pair_count++;

                }

                else
                {
                    for(int k = 0; k < pair_count; k++)
                    {
                        if ((pairs[k].winner != i && pairs[k].loser != j) || (pairs[k].winner != j && pairs[k].loser != i))
                        {
                            pairs[pair_count].winner = i;
                            pairs[pair_count].loser = j;
                            pair_count++;
                            break;
                        }
                    }
                }
            }

            else if ((preferences[i][j] < preferences[j][i]) && j > i)
            {
                if (pair_count == 0)
                {
                    pairs[pair_count].winner = j;
                    pairs[pair_count].loser = i;
                    pair_count++;

                }

                else
                {

                    for(int k = 0; k < pair_count; k++)
                    {
                        if ((pairs[k].winner != j && pairs[k].loser != i) || (pairs[k].winner != i && pairs[k].loser != j))
                        {
                            pairs[pair_count].winner = j;
                            pairs[pair_count].loser = i;
                            pair_count++;
                            break;
                        }
                    }
                }
            }
        }
    }
    return;
}

// Sort pairs in decreasing order by strength of victory
void sort_pairs(void)
{
    //KYA KAISE HO RAHA WO PAHELE WALE MEIN EXPLAIN KAR DIYA HAI
    int temp_array[pair_count];

    for (int i = 0; i < pair_count; i++)
    {
        temp_array[i] = preferences[pairs[i].winner][pairs[i].loser];
    }

    printf("\n");
    printf("----ADDED PAIRS VALUES----");
    printf("\n");

    for (int i = 0; i < pair_count; i++)
    {
        printf("%i\n", temp_array[i]);
    }

    bubble_sort(temp_array);

    printf("\n");
    printf("----ADDED PAIRS VALUES(SORTED)----");
    printf("\n");

    for (int i = 0; i < pair_count; i++)
    {
        printf("%i\n", temp_array[i]);
    }

    pair sorted_pairs[MAX * (MAX - 1) / 2];

    //to access the pairs array
    for (int i = 0; i < pair_count; i++)
    {
        //storing the integer value of the pairs array
        int compare_num = preferences[pairs[i].winner][pairs[i].loser];
        //loop to access temp_array
        for (int j = 0; j < pair_count; j++)
        {
            if (temp_array[j] == compare_num)
            {
                sorted_pairs[j].winner = pairs[i].winner;
                sorted_pairs[j].loser = pairs[i].loser;
                //a value that is for sure not going to match
                temp_array[j] = 0;
                break;
            }
        }
    }

    for (int i = 0; i < pair_count; i++)
    {
        pairs[i].winner = sorted_pairs[i].winner;
        pairs[i].loser = sorted_pairs[i].loser;
    }
    return;
}

void bubble_sort(int array[])
{
    int swap_count = 0;
    for (int i = 0; i < (pair_count - 1); i++)
    {
        if (array[i] < array[i + 1])
        {
            int swap_var = array[i];
            array[i] = array[i + 1];
            array[i + 1] = swap_var;
            swap_count++;
        }
    }

    if (swap_count == 0)
    {
        return;
    }

    bubble_sort(array);
}


// Lock pairs into the candidate graph in order, without creating cycles
void lock_pairs(void)
{
    //sabse pahele jo temporary locked table banaye thi usme sab data false kiya jisse wo ekdam same to same locked table ke jaise ho jaye
    for (int i = 0; i < candidate_count; i++)
    {
        for (int j = 0; j < candidate_count; j++)
        {
            temp_locked[i][j] = false;
        }
    }

   for (int i = 0; i < pair_count; i++)
    {
        //temp locked mein arrow banao phir us arrow ke baare mein jaankari no_winner() karega ki kya karna hai
        temp_locked[pairs[i].winner][pairs[i].loser] = true;

        //agar no_winner nhi hai, no winner means koi winner nhi hai means jab infinite cycle ban rahi hai to agar not no winner, ki no winner nhi hai hence koi winner hai to main locked mein bhi changes kardo wahi wale to temp_locked mein kiya hai hence maine main wali locked table mein changes nhi kiya and sirf wahi kiya jo infinite cycle nhi bana raha and jo bana rahe unko ek doosri table ke sahare delete karwa diya
        if (!no_winner(i))
        {
            locked[pairs[i].winner][pairs[i].loser] = true;
        }
    }
    return;
}

// Print the winner of the election
void print_winner(void)
{
    for (int i = 0; i < candidate_count; i++)
    {
        int verify = 0;
        for (int j = 0; j < candidate_count; j++)
        {
            if (locked[j][i] == true)
            {
                 verify++;
                 break;
            }
        }
        if (verify == 0)
        {
            printf("%s\n", candidates[i]);
        }
    }
    return;
}

//logic wahi same hai jo pahele wale mein lagaya tha but pahele wale mein main locked table mein changes kar rahe the and and isme ek doosri table mein and banki sab bilkul same hai
//PAHELE WALE MEIN LINE DRAW HO RAHI MAIN LOCKED MEIN AND PHIR WO DELETE HO RAHI TO MUJHE LAGA KI SHAYAD UNKO MAIN TABLE SE DIKKAT HAI TO EK DOOSRI TABLE MEIN SAME LOGIC APPLY KIYA AND ISSE MAIN WALE MEIN KEVAL WAHI CONTENT PHUNCHA JO KI INFINTE CYCLE NHI BANA RAHA BUT ISSUE UNKI REQUIREMENTS SATISFIED NHI HUII
bool no_winner(int affected_index)
{
    int verify = 0;
    for (int i = 0; i < candidate_count; i++)
    {
        for (int j = 0; j < candidate_count; j++)
        {
            if (temp_locked[j][i] == true)
            {
                verify++;
                break;
            }
        }
    }
    if (verify == candidate_count)
    {
        temp_locked[pairs[affected_index].winner][pairs[affected_index].loser] = false;
        return true;
    }
    else
    {
        return false;
    }
}



```

Problem with the above self created codes - [Here](https://youtu.be/fKDMQkMcKFI)

### Code by GPT which works perfectly fine just like the above codes but also overcome the issue explained in the issue above

```c
#include <cs50.h>       // Include the CS50 library for get_string and get_int functions
#include <stdio.h>      // Include the standard input-output library for printf
#include <string.h>     // Include the string library for string operations like strcmp

// Max number of candidates
#define MAX 9

// preferences[i][j] is the number of voters who prefer candidate i over candidate j
int preferences[MAX][MAX];

// locked[i][j] means candidate i is locked in over candidate j
bool locked[MAX][MAX];

// Each pair has a winner and a loser
typedef struct
{
    int winner;
    int loser;
} pair;

// Array of candidates
string candidates[MAX];
// Array of pairs
pair pairs[MAX * (MAX - 1) / 2];

// Number of pairs
int pair_count;
// Number of candidates
int candidate_count;

// Function prototypes
bool vote(int rank, string name, int ranks[]);
void record_preferences(int ranks[]);
void add_pairs(void);
void sort_pairs(void);
void lock_pairs(void);
void print_winner(void);
bool has_cycle(int end, int start);

int main(int argc, string argv[])
{
    // Check for invalid usage
    if (argc < 2)
    {
        // Print usage message if not enough arguments
        printf("Usage: tideman [candidate ...]\n");
        return 1;
    }

    // Populate array of candidates
    candidate_count = argc - 1; // Set the number of candidates
    if (candidate_count > MAX)
    {
        // Check if the number of candidates exceeds the maximum allowed
        printf("Maximum number of candidates is %i\n", MAX);
        return 2;
    }
    for (int i = 0; i < candidate_count; i++)
    {
        // Assign candidate names from command line arguments
        candidates[i] = argv[i + 1];
    }

    // Clear graph of locked in pairs
    for (int i = 0; i < candidate_count; i++)
    {
        for (int j = 0; j < candidate_count; j++)
        {
            // Initialize all entries in the locked matrix to false
            locked[i][j] = false;
        }
    }

    pair_count = 0; // Initialize pair count to 0
    int voter_count = get_int("Number of voters: "); // Get number of voters

    // Query for votes
    for (int i = 0; i < voter_count; i++)
    {
        // ranks[i] is voter's ith preference
        int ranks[candidate_count]; // Array to store ranks of the candidates for the voter

        // Query for each rank
        for (int j = 0; j < candidate_count; j++)
        {
            // Get the candidate name for rank j+1
            string name = get_string("Rank %i: ", j + 1);

            // Validate the vote
            if (!vote(j, name, ranks))
            {
                printf("Invalid vote.\n");
                return 3;
            }
        }

        // Record the voter's preferences in the global preferences matrix
        record_preferences(ranks);

        printf("\n");
    }

    // Process pairs and determine the winner
    add_pairs();   // Add pairs of candidates
    sort_pairs();  // Sort the pairs by strength of victory
    lock_pairs();  // Lock the pairs into the candidate graph
    print_winner(); // Print the winner of the election

    return 0;
}

// Update ranks given a new vote
bool vote(int rank, string name, int ranks[])
{
    // Loop through all candidates
    for (int i = 0; i < candidate_count; i++)
    {
        // Compare input name with candidate names
        if (strcmp(name, candidates[i]) == 0)
        {
            // If the candidate is found, update the rank
            ranks[rank] = i;
            return true;
        }
    }
    // Return false if the candidate name is not found
    return false;
}

// Update preferences given one voter's ranks
void record_preferences(int ranks[])
{
    /*

    ISME SIMPLE BASIC BAS YE KIYA GYA HAI KI candidate over candidate check karo and ranks array mein jaise values hai waise add karna hai simple uhm nhi samaj aaya hoga to ruko

    jaise ranks aise hai c, b, d, a to yani ki agar hum isko index mein dekhe to 2, 1, 3, 0

    to simple wo start karga i se and uske baad wala lega rank mein and kuch steps tak i static rahega and j dynamic rahega

    to ranks[2,1,3,0] mein indexing hai 0,1,2,3

    to i = 0, j = 1, 2, 3

    matlab ki jo position hai upar wo i represent kar raha - kaun sa prefered hai and kinke upar prefered hai wo j kar raha

    to sirf is array mein baat kare agar to i = 0, j = 1 then 2 then 3 HENCE

    pf = preferences

    pf[0][1]++ then pf[0][2]++ then pf[0][3]++

    and iske baad i = 1 hence j = 2 then 3

    pf[1][2]++ then pf[1][3]

    kyonki 1 position pe jo hai wo 0 position wale se prefer to nhi kiya gya hai and iske aagge

    pf[2][3] bas, 3rd wala last hai jisko kisi se prefer nhi kiya to preference table kuch aise banegi with column head as prefered and row head as prefered over

        0    1    2    3
    ---------------------
    0 | 0    0    0    0
      |
    1 | 1    0    0    0
      |
    2 | 1    1    0    0
      |
    3 | 1    1    1    0


    */
    // Loop through each rank given by the voter
    for (int i = 0; i < candidate_count; i++)
    {
        // Loop through the ranks following the current rank
        for (int j = i + 1; j < candidate_count; j++)
        {
            // Update the preferences matrix
            preferences[ranks[i]][ranks[j]]++;
        }
    }
}

// Record pairs of candidates where one is preferred over the other
void add_pairs(void)
{
    /*

    simple upar wala table consider karo and neeche likhe if statement se match karoge to paoge ki wo har ek cross value ko compare kar rahe, ek time par

    pf[0][1] > pf[1][0] and aise hi ek time par aayega pf[1][0] > pf[0][1]

    hence kuch hi chand lines mein usne pairs bana diya jo ki tumne poora LGTV color ke brackets bana kar kiya

    */
    // Loop through all candidate pairs
    for (int i = 0; i < candidate_count; i++)
    {
        for (int j = 0; j < candidate_count; j++)
        {
            // Check if candidate i is preferred over candidate j
            if (preferences[i][j] > preferences[j][i])
            {
                // Record the winner and loser in pairs array
                pairs[pair_count].winner = i;
                pairs[pair_count].loser = j;
                pair_count++; // Increment the pair count
            }
        }
    }
}

// Sort pairs in decreasing order by strength of victory
void sort_pairs(void)
{
    //SORTING MEIN BUBBLE SORT KA USE KIYA HAI TO THORA SA DIMAAG LAGAKE KI NEECHE DIYE GYEE PROMPTS CONDITIONAL STATEMENTS KO SAMAJ LO AND BAS
    // Simple bubble sort algorithm to sort pairs
    for (int i = 0; i < pair_count - 1; i++)
    {
        for (int j = 0; j < pair_count - i - 1; j++)
        {
            // Calculate the strength of victory for each pair
            int strength1 = preferences[pairs[j].winner][pairs[j].loser] - preferences[pairs[j].loser][pairs[j].winner];
            int strength2 = preferences[pairs[j + 1].winner][pairs[j + 1].loser] - preferences[pairs[j + 1].loser][pairs[j + 1].winner];

            // Swap pairs if the next pair is stronger
            if (strength2 > strength1)
            {
                pair temp = pairs[j];
                pairs[j] = pairs[j + 1];
                pairs[j + 1] = temp;
            }
        }
    }
}

/*

THE TWO MOST IMPORTANT FUNCTION TO LEARN ABOUT THEIR WORKING ARE THESE TWO

*/

// Lock pairs into the candidate graph in order, without creating cycles
void lock_pairs(void)
{
    // Loop through all pairs
    for (int i = 0; i < pair_count; i++)
    {
        // Ensure no cycle is created
        //not has_cycle means agar cycle nhi ban rahi hai tab line draw kar do
        //loser means jis par line end hogi and winner means jis se line start hogi
        if (!has_cycle(pairs[i].loser, pairs[i].winner))
        {
            // Lock the pair
            locked[pairs[i].winner][pairs[i].loser] = true;
        }
    }
}

// Check for cycles in the graph
bool has_cycle(int end, int start)
{
    // Base case: if we cycle back to the start
    //agar end and start same hogye means ki winner and loser same person aagya means cycle poori hence hence yaha gadbad hai
    //ab has_cycle hai means true return karna padega
    if (end == start)
    {
        return true;
    }
    // Loop through all candidates
    for (int i = 0; i < candidate_count; i++)
    {
        // Check if end is locked in over i
        // ab loser aaya hai end mein and uska har us bande se consider karenge jiske sath uska true hai

        /*
        means agar a,b,c,d candidates hai and a and b ke beech mein mukabla huaa and a prefered over b hai to a se b ki or line khinchi hogi and then b se dekhenge ki kiski or line khinchi and maan lete hai c ki or khinchi hai pahele se and c se wapis a ki or khinchi hai means ki is line ko draw karne se cycle ban jayegi hai hame wo nhi chaiye to jaise hi waapis se a aayega c ke baad, end == start ho jayega and program left ho jayega, upar a to b nhi khinchegi and hence pair skip ho gya
        */
        if (locked[end][i])
        {
            // Recursively check for cycles
            // a se b ka dekhna start kiya ki cycle banana hai kya, to bhi b se jiski bhi gyii usse a ki dekha phir kaheki ye recursive hai b se jiski tarf gayii hai, usse jiski taraf gayii hogi usse a tak dekhenge and agar a nhi mila hence cycle nhi milegi hence usko draw kar sakte hai
            //agar abhi bhi na samaj aaye ho to upar jo bhi content likha hai isko follow karo draw karte karte to samaj mein aa jayegaa
            if (has_cycle(i, start))
            {
                return true;
            }
        }
    }
    // Return false if no cycle is found
    return false;
}

// Print the winner of the election
void print_winner(void)
{
    // Loop through all candidates
    for (int i = 0; i < candidate_count; i++)
    {
        bool found = false;
        // Check if there is any candidate locked over i
        for (int j = 0; j < candidate_count; j++)
        {
            if (locked[j][i])
            {
                found = true;
                break;
            }
        }
        // If no candidate is locked over i, i is the source
        if (!found)
        {
            // Print the winner
            printf("%s\n", candidates[i]);
            return;
        }
    }
}
```

> [!NOTE]
> How this works ?

- As there are multiple codes, working of each is present in each code's comments
