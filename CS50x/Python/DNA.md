# Question
DNA, the carrier of genetic information in living things, has been used in criminal justice for decades. But how, exactly, does DNA profiling work? Given a sequence of DNA, how can forensic investigators identify to whom it belongs?

In a file called `dna.py`, implement a program that identifies to whom a sequence of DNA belongs.

# Assumed Output
```terminal
$ python dna.py databases/small.csv sequences/1.txt                
Bob
$ python dna.py databases/small.csv sequences/2.txt                      
No match
$ python dna.py databases/large.csv sequences/19.txt                 
Fred  
```

# Background
DNA is really just a sequence of molecules called nucleotides, arranged into a particular shape (a double helix). Every human cell has billions of nucleotides arranged in sequence. Each nucleotide of DNA contains one of four different bases: adenine (A), cytosine (C), guanine (G), or thymine (T). Some portions of this sequence (i.e., genome) are the same, or at least very similar, across almost all humans, but other portions of the sequence have a higher genetic diversity and thus vary more across the population.

One place where DNA tends to have high genetic diversity is in Short Tandem Repeats (STRs). An STR is a short sequence of DNA bases that tends to repeat consecutively numerous times at specific locations inside of a person’s DNA. The number of times any particular STR repeats varies a lot among individuals. In the DNA samples below, for example, Alice has the STR `AGAT` repeated four times in her DNA, while Bob has the same STR repeated five times.

![](https://i.imgur.com/PnMIro7.png)

Using multiple STRs, rather than just one, can improve the accuracy of DNA profiling. If the probability that two people have the same number of repeats for a single STR is 5%, and the analyst looks at 10 different STRs, then the probability that two DNA samples match purely by chance is about 1 in 1 quadrillion (assuming all STRs are independent of each other). So if two DNA samples match in the number of repeats for each of the STRs, the analyst can be pretty confident they came from the same person. CODIS, the FBI’s [DNA database](https://www.fbi.gov/services/laboratory/biometric-analysis/codis/codis-and-ndis-fact-sheet), uses 20 different STRs as part of its DNA profiling process.

What might such a DNA database look like? Well, in its simplest form, you could imagine formatting a DNA database as a CSV file, wherein each row corresponds to an individual, and each column corresponds to a particular STR.

```csv
name,AGAT,AATG,TATC
Alice,28,42,14
Bob,17,22,19
Charlie,36,18,25
```

The data in the above file would suggest that Alice has the sequence `AGAT` repeated 28 times consecutively somewhere in her DNA, the sequence `AATG` repeated 42 times, and `TATC` repeated 14 times. Bob, meanwhile, has those same three STRs repeated 17 times, 22 times, and 19 times, respectively. And Charlie has those same three STRs repeated 36, 18, and 25 times, respectively.

So given a sequence of DNA, how might you identify to whom it belongs? Well, imagine that you looked through the DNA sequence for the longest consecutive sequence of repeated `AGAT`s and found that the longest sequence was 17 repeats long. If you then found that the longest sequence of `AATG` is 22 repeats long, and the longest sequence of `TATC` is 19 repeats long, that would provide pretty good evidence that the DNA was Bob’s. Of course, it’s also possible that once you take the counts for each of the STRs, it doesn’t match anyone in your DNA database, in which case you have no match.

In practice, since analysts know on which chromosome and at which location in the DNA an STR will be found, they can localize their search to just a narrow section of DNA. But we’ll ignore that detail for this problem.

Your task is to write a program that will take a sequence of DNA and a CSV file containing STR counts for a list of individuals and then output to whom the DNA (most likely) belongs.

# How this works ?
[Click here for Visual Explanation](https://youtu.be/efosH35-lUg)

# Solution
```python
import csv
import sys

def main():

    # Check for command-line usage
    # initialize the variables here to make them global
    db_src = " "
    seq_src = " "

    # will check if is possible to get command line arguments
    try:
        db_src = sys.argv[1]
        seq_src = sys.argv[2]
    except IndexError:
        print("Usage: python dna.py [database.csv] [sequence.txt]")


    #Read database file into a variable

    database = [] # list which is going to be the list of dictionaries
    with open(db_src) as db_csv:
        database_src = csv.DictReader(db_csv) # will open the csv file as a dictionary object
        for row in database_src:
            database.append(row) #adding each row of csv file as a dictionary in the list

    #Read DNA sequence file into a variable

    src_sequence = " "
    with open(seq_src) as seq_txt:
        src_sequence = seq_txt.read()

    # Find longest match of each STR in DNA sequence

    keys_list = list(database_src.fieldnames) # gives the list of the keys or basically the column headers of csv file

    # initalizing the dictionary which will be used to store the repetitions of the substrings
    check_dict = {}
    for seq in range(1, len(keys_list)):
        num = longest_match(src_sequence, keys_list[seq])
        check_dict[keys_list[seq]] = str(num) #inserting the data into the checker dictionary


    # Check database for matching profiles

    match_found = False # boolean value being used to make sure if the sequence has a match or not
    for i in range(len(database)):
        src_match_dict = {} # a dictionary that is storing the data from database dictionary to compare and see if it the dna is matched or not
        for x in range(1, len(database[i])): # adding the content in the src_match_dictionary
            src_match_dict[keys_list[x]] = database[i][keys_list[x]] # consider the video to get this explanantion

        if src_match_dict == check_dict:
            print(f"{database[i]['name']}")
            match_found = True
            break

    if match_found == False:
        print("No Match")
    return


def longest_match(sequence, subsequence):
    """Returns length of longest run of subsequence in sequence."""

    # Initialize variables
    longest_run = 0
    subsequence_length = len(subsequence)
    sequence_length = len(sequence)

    # Check each character in sequence for most consecutive runs of subsequence
    for i in range(sequence_length):

        # Initialize count of consecutive runs
        count = 0

        # Check for a subsequence match in a "substring" (a subset of characters) within sequence
        # If a match, move substring to next potential match in sequence
        # Continue moving substring and checking for matches until out of consecutive matches
        while True:

            # Adjust substring start and end
            start = i + count * subsequence_length
            end = start + subsequence_length

            # If there is a match in the substring
            if sequence[start:end] == subsequence:
                count += 1

            # If there is no match in the substring
            else:
                break

        # Update most consecutive matches found
        longest_run = max(longest_run, count)

    # After checking for runs at each character in seqeuence, return longest run found
    return longest_run

main()

```
