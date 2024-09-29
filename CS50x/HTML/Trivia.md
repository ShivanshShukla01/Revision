# Question
Write a webpage that lets users answer trivia questions.

![](https://i.imgur.com/4LjzjPz.png)

# Implementation
Design a webpage using HTML, CSS, and JavaScript to let users answer trivia questions.

- In `index.html`, add beneath “Part 1” a multiple-choice trivia question of your choosing with HTML.
    - You should use an `h3` heading for the text of your question.
    - You should have one `button` for each of the possible answer choices. There should be at least three answer choices, of which exactly one should be correct.
- Using JavaScript, add logic so that the buttons change colors when a user clicks on them.
    - If a user clicks on a button with an incorrect answer, the button should turn red and text should appear beneath the question that says “Incorrect”.
    - If a user clicks on a button with the correct answer, the button should turn green and text should appear beneath the question that says “Correct!”.
- In `index.html`, add beneath “Part 2” a text-based free response question of your choosing with HTML.
    - You should use an `h3` heading for the text of your question.
    - You should use an `input` field to let the user type a response.
    - You should use a `button` to let the user confirm their answer.
- Using JavaScript, add logic so that the text field changes color when a user confirms their answer.
    - If the user types an incorrect answer and presses the confirmation button, the text field should turn red and text should appear beneath the question that says “Incorrect”.
    - If the user types the correct answer and presses the confirmation button, the input field should turn green and text should appear beneath the question that says “Correct!”.

# Solution
### HTML
```html
<!DOCTYPE html>

<html lang="en">
    <head>
        <link href="https://fonts.googleapis.com/css2?family=Montserrat:wght@500&display=swap" rel="stylesheet">
        <link href="styles.css" rel="stylesheet">
        <title>Trivia!</title>
        <script>
            // TODO: Add code to check answers to questions
        </script>
    </head>
    <body>
        <div class="header">
            <h1>Trivia!</h1>
        </div>

        <div class="container">
            <div class="section">
                <h2>Part 1: Multiple Choice </h2>
                <hr>
                <!-- TODO: Add multiple choice question here -->
                 <h3>Which of the following Programming Language is primarily used of Web Developement?</h3>
                 <p id="mcqresult"></p>
                 <div class="options">
                     <button class="btns" onclick="mcqverify(this)">JAVA</button>
                     <button class="btns" onclick="mcqverify(this)">Python</button>
                     <button class="btns" onclick="mcqverify(this)">C++</button>
                     <button class="btns" onclick="mcqverify(this)">HTML</button>
                     <button class="btns" onclick="mcqverify(this)">Kotlin</button>
                    </div>
                </div>
                
            <div class="section">
                <h2>Part 2: Free Response</h2>
                <hr>
                <!-- TODO: Add free response question here -->
                <h3>In India, which state has highest population density?</h3>
                
                <p id="txtresult"></p>
                <input type="text" name="state" id="inputbox" placeholder="Type here">
                <button type="submit" class="smtbtn" onclick="txtverify()">Submit</button>
            </div>
        </div>
        <script>
            function mcqverify(button)
            {
                if (button.innerHTML === 'HTML'){
                   document.getElementById("mcqresult").innerHTML = 'Correct!';
                   button.style.backgroundColor = 'lime';
                }
                else {
                    document.getElementById("mcqresult").innerHTML = 'Incorrect!';
                    button.style.backgroundColor = 'red';
                }
            }

            function txtverify()
            {
                let inputarea = document.getElementById('inputbox');
                let userinput = inputarea.value.toLowerCase();

                if (userinput === 'bihar'){
                    inputarea.style.backgroundColor = 'lime';
                    document.getElementById('txtresult').innerHTML = 'Correct';
                } 
                else {
                    inputarea.style.backgroundColor = 'red';
                    document.getElementById('txtresult').innerHTML = 'Incorrect';
                }
            }
        </script>
    </body>
</html>

```

### CSS
```css
body {
    background-color: #fff;
    color: #212529;
    font-size: 1rem;
    font-weight: 400;
    line-height: 1.5;
    margin: 0;
    text-align: left;
}

.container {
    margin-left: auto;
    margin-right: auto;
    padding-left: 15px;
    padding-right: 15px;
}

.header {
    background-color: #477bff;
    color: #fff;
    margin-bottom: 2rem;
    padding: 2rem 1rem;
    text-align: center;
}

.section {
    padding: 0.5rem 2rem 1rem 2rem;
}

.section:hover {
    background-color: #f5f5f5;
    transition: color 2s ease-in-out, background-color 0.15s ease-in-out;
}

h1 {
    font-family: 'Montserrat', sans-serif;
    font-size: 48px;
}

button, input[type="submit"] {
    background-color: #d9edff;
    border: 1px solid transparent;
    border-radius: 0.25rem;
    font-size: 0.95rem;
    font-weight: 400;
    line-height: 1.5;
    padding: 0.375rem 0.75rem;
    text-align: center;
    transition: color 0.15s ease-in-out, background-color 0.15s ease-in-out, border-color 0.15s ease-in-out, box-shadow 0.15s ease-in-out;
    vertical-align: middle;
}


input[type="text"] {
    line-height: 1.8;
    width: 25%;
}

input[type="text"]:hover {
    background-color: #f5f5f5;
    transition: color 2s ease-in-out, background-color 0.15s ease-in-out;
}

.options
{
    display: flex;
    justify-content: space-between;
    align-items: center;
    padding-left: 50px;
    padding-right: 50px;
}

.btns:hover
{
    background-color: #b9d6f0;
}

.smtbtn:hover
{
    background-color: #b9d6f0;
}

#mcqresult
{
    align-items: center;
}
```

### Creating Event Listeners using JavaScript
```html
<!DOCTYPE html>

<html lang="en">
    <head>
        <link href="https://fonts.googleapis.com/css2?family=Montserrat:wght@500&display=swap" rel="stylesheet">
        <link href="styles.css" rel="stylesheet">
        <title>Trivia!</title>
        <script>

            // Wait for DOM content to load
            document.addEventListener('DOMContentLoaded', function() {

                // Get all elements with class "correct"
                let corrects = document.querySelectorAll('.correct');

                // Add event listeners to each correct button
                for (let i = 0; i < corrects.length; i++) {
                    corrects[i].addEventListener('click', function() {

                        // Set background color to green
                        corrects[i].style.backgroundColor = 'Green';

                        // Go to parent element of correct button and find the first element with class "feedback" which has that parent
                        corrects[i].parentElement.querySelector('.feedback').innerHTML = 'Correct!';
                    });
                }

                // When any incorrect answer is clicked, change color to red.
                let incorrects = document.querySelectorAll(".incorrect");
                for (let i = 0; i < incorrects.length; i++) {
                    incorrects[i].addEventListener('click', function() {

                        // Set background color to red
                        incorrects[i].style.backgroundColor = 'Red';

                        // Go to parent element of correct button and find the first element with class "feedback" which has that parent
                        incorrects[i].parentElement.querySelector('.feedback').innerHTML = 'Incorrect';
                    });
                }

                // Check free response submission
                document.querySelector('#check').addEventListener('click', function() {
                    let input = document.querySelector('input');
                    if (input.value === 'Switzerland') {
                        input.style.backgroundColor = 'green';
                        input.parentElement.querySelector('.feedback').innerHTML = 'Correct!';
                    }
                    else {
                        input.style.backgroundColor = 'red';
                        input.parentElement.querySelector('.feedback').innerHTML = 'Incorrect';
                    }
                });
            });
        </script>
    </head>
    <body>
        <div class="header">
            <h1>Trivia!</h1>
        </div>

        <div class="container">
            <div class="section">
                <h2>Part 1: Multiple Choice </h2>
                <hr>
                <h3>What is the approximate ratio of people to sheep in New Zealand?</h3>
                <button class="incorrect">6 people per 1 sheep</button>
                <button class="incorrect">3 people per 1 sheep</button>
                <button class="incorrect">1 person per 1 sheep</button>
                <button class="incorrect">1 person per 3 sheep</button>
                <button class="correct">1 person per 6 sheep</button>
                <p class="feedback"></p>
            </div>

            <div class="section">
                <h2>Part 2: Free Response</h2>
                <hr>
                <h3>In which country is it illegal to own only one guinea pig, as a lone guinea pig might get lonely?</h3>
                <input type="text"></input>
                <button id="check">Check Answer</button>
                <p class="feedback"></p>
            </div>
        </div>
    </body>
</html>
```

### Creating Event Listeners using HTML
```html
<!DOCTYPE html>

<html lang="en">
    <head>
        <link href="https://fonts.googleapis.com/css2?family=Montserrat:wght@500&display=swap" rel="stylesheet">
        <link href="styles.css" rel="stylesheet">
        <title>Trivia!</title>
        <script>
            function checkMultiChoice(event) {

                // Get the element which triggered the event
                let button = event.target;

                // Check if the element's inner HTML matches expected answer
                if (button.innerHTML == '1 person per 6 sheep') {
                    button.style.backgroundColor = 'Green';
                    button.parentElement.querySelector('.feedback').innerHTML = 'Correct!';
                }
                else {
                    button.style.backgroundColor = 'Red';
                    button.parentElement.querySelector('.feedback').innerHTML = 'Incorrect';
                }
            }

            function checkFreeResponse(event) {

                // Get the element which triggered the event
                let button = event.target;

                // Get the input element corresponding to the button
                let input = button.parentElement.querySelector('input');

                // Check for correct answer
                if (input.value === 'Switzerland') {
                    input.style.backgroundColor = 'Green';
                    input.parentElement.querySelector('.feedback').innerHTML = 'Correct!';
                }
                else {
                    input.style.backgroundColor = 'Red';
                    input.parentElement.querySelector('.feedback').innerHTML = 'Incorrect';
                }
            }
        </script>
    </head>
    <body>
        <div class="header">
            <h1>Trivia!</h1>
        </div>

        <div class="container">
            <div class="section">
                <h2>Part 1: Multiple Choice </h2>
                <hr>
                <h3>What is the approximate ratio of people to sheep in New Zealand?</h3>
                <button onclick="checkMultiChoice(event)">6 people per 1 sheep</button>
                <button onclick="checkMultiChoice(event)">3 people per 1 sheep</button>
                <button onclick="checkMultiChoice(event)">1 person per 1 sheep</button>
                <button onclick="checkMultiChoice(event)">1 person per 3 sheep</button>
                <button onclick="checkMultiChoice(event)">1 person per 6 sheep</button>
                <p class="feedback"></p>
            </div>

            <div class="section">
                <h2>Part 2: Free Response</h2>
                <hr>
                <h3>In which country is it illegal to own only one guinea pig, as a lone guinea pig might get lonely?</h3>
                <input type="text"></input>
                <button onclick="checkFreeResponse(event)">Check Answer</button>
                <p class="feedback"></p>
            </div>
        </div>
    </body>
</html>
```