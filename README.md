<h1><a href="https://academy.postman.com/project-ai-text-summarizer/86687">Project-Based Learning: Build an AI Text Summarizer app</a></h1>
<h3>Tech stack</h3>
<p>These are the tools and technologies that we’ll be using to build our app!</p>

<li>API platform - Postman</li>

<li>Code Editor/Deployment tool - Replit </li>

<li>Back end - Node.js,Express js,Hugging Face Inference API</li>

<li>Front end - HTML,CSS,JavaScript</li>
<br/>
<h1>Structure of the app</h1>
<p>It’s important to understand the components that will go into the app we’re building.

Our app will have a client side (website), server side, and external API integration to Hugging Face.
</p>

<img src="https://github.com/user-attachments/assets/f83aea9e-6960-4426-a0e2-36829e7a815e"/>
<br/>
<h3><a href="https://academy.postman.com/project-ai-text-summarizer/86582">Understand the server</a></h3>

<p>A server “serves” content on the web. The server will listen for incoming requests from clients (such as web browsers), handle them and send responses. Eventually our server will have an endpoint that our front end website can call to fetch a text summary from the Hugging Face Inference API (we’ll code that part together later!).

The server in index.js uses Express, which is a NodeJS web application framework that makes it easier to build servers.</p>

<p>Open the file named "index.js". It should look like this:</p>

```js
const express = require('express');
const app = express();
const port = 3000;


// Parses JSON bodies (as sent by API clients)
app.use(express.json());

// Serves static files from the 'public' directory
app.use(express.static('public'));

// Start the server
app.listen(port, () => {
  console.log("Server running at http://localhost:${port}/");
});
 ```

<p>The first three lines import the express module, create an Express app instance, and set the local port to 3000 used by Replit. Replit does all the work for us in exposing our server to the public internet!
On line 6, we are setting our server up with built-in Express middleware that adds the ability to parse JSON bodies. We will need this later when we code an API endpoint on our server that needs to accept JSON request bodies.
On line 9, we are serving the “public” folder in our project directory to a root endpoint (“<your Replit URL>/”). The public folder contains the code for our website. That’s why we see the website when we access your Replit URL!
Up until this point we were configuring the server. Most importantly, on line 12 we finally start the server with app.listen().</p>
  
<h3><a href="https://academy.postman.com/project-ai-text-summarizer/93816">Add a /summarize endpoint to the server</a></h3>
Table of Contents
<ul>
<li>Milestone 1 - Add a /summarize endpoint in index.js</li>
<li>Milestone 2 - Make a summarizeText() function</li>
<li>Milestone 3 - Understand the Postman request to call Hugging Face</li>
<li>Milestone 4 - Get a Hugging Face access token</li>
<li>Milestone 5 - Make an authorized call to Hugging Face in Postman</li>
<li>Milestone 6 - Generate code in Postman to call Hugging Face in our app</li>
<li>Milestone 7 - Modify the code snippet for our app</li>
<li>Milestone 8 - Finish the POST /summarize endpoint</li>
What we will do in this section
</ul>
<p>
Add a summarize endpoint to our server to accept POST requests from the front end website
Create a function that calls the Hugging Face Inference API to summarize text with AI
In this section, you’ll be adding logic to the server that will enable the back end of our app to make an external API call to Hugging Face to get summarized text.

Why don’t we just call the Hugging Face Inference API directly from our front end web app to get a summary? The reason is that calling the Hugging Face Inference API requires using a secret token, and it is impossible to hide secrets in the front end! Hackers can always inspect the browser and find your secrets and use (and abuse!) them.

Therefore, whenever you want to make sensitive API calls, you’ll want to do it from the back end (server) side of your applications because you can hide secrets safely there. Replit has a great secrets feature for this we will use later.
</p>

<h3>Milestone 1 - Add a /summarize endpoint in index.js</h3>
<p>Right now, our server in index.js only has one endpoint (the root or “/”) that serves our website on line 9.

We want to add another endpoint called /summarize that will accept POST requests from our front end website that contain the text to summarize. Yes - we are building an API!

In Express, you can easily add routes that are endpoints for the server to listen for using the syntax:

app.<HTTP method>('/<endpoint>', (req, res) => { /* handle request (req) and response (res) … })

Let’s add a POST endpoint called /summarize that will eventually securely call the Hugging Face Inference API to summarize our text.

Copy the following code and paste it in index.js on the line below “app.use(express.static('public'))”, but BEFORE the line “app.listen(...)”</p>

Snippet 3.3a)

// Handle POST requests to the '/summarize' endpoint

```js
app.post('/summarize', (req, res) => {

   // TODO: handle POST /summarize request


});
```
<p>This code configures our server to listen for POST requests to the "/summarize" endpoint. Right now this endpoint does nothing because we haven’t coded anything to handle requests and send responses yet! We’ll start working on that in the next section!</p>

<h3>Milestone 2 - Make a summarizeText() function</h3>
<p>
Next we are going to define a function that will eventually call the Hugging Face Inference API to summarize text. We will use this function when handling requests in our /summarize endpoint on our server .

To keep our code clean, we will code the function in a separate file called summarize.js.

To start, open the file named summarize.js in the project root directory. The file should be empty right now!

Our function will be called summarizeText() and will be asynchronous, meaning we want our code to know to wait for a response from Hugging Face before continuing. We do this in JavaScript by using the async keyword when defining a function.

Paste this code into summarize.js to start defining our summarizeText() function. We will pass an argument called text into our function, which will be the text we are trying to summarize. The last line is important because it exports our function from the file so we can import it into our server later.
</p>
Snippet 3.3b)

// This is the function where the call to the API is made. Returns the summarized text as a string.

```js
async function summarizeText(text) {

  // INSERT CODE SNIPPET FROM POSTMAN BELOW

}

// Allows for summarizeText() to be called outside of this file

module.exports = summarizeText;
 ```

<h3>Milestone 3 - Understand the Postman request to call Hugging Face</h3>
<p>
We want to use the Hugging Face Inference API to access Facebook’s “bart-large-cnn” model that specializes in summarizing text. Before making an API call with code, it is best to explore how the API works and get a successful response using Postman. In Postman, navigate to your forked collection from earlier (or fork it again here).

In your forked collection, select the hugging face summary request in the “backend” folder. This request calls the Hugging Face Inference API and returns a summary of the text passed in.

hugging face request

You will see that this request is a POST request to the endpoint "https://api-inference.huggingface.co/models/facebook/bart-large-cnn" with a Body that sends some JSON data.

The Hugging Face Inference API lets you call most models from Hugging Face’s open source models list for free using the endpoint https://api-inference.huggingface.co/models/<MODEL NAME>. As mentioned above, we are going to use Facebook’s “bart-large-cnn” model (model name: facebook/bart-large-cnn) to summarize text.

You can see the various request body shapes that different types of models expect in this list.

The request already has a Body set up that sends “inputs” (the text we want to summarize) and some parameters that specify the minimum and maximum length of the summary we want from the model.

But if you try to send this request now, you will get a 400 level error because using the Hugging Face Inference API requires authorization with an access token! Let’s get a token in the next step.
</p>
 

<h3>Milestone 4 - Get a Hugging Face access token</h3>
<p>
Before we can call the Hugging Face Inference API, we’ll need an access token.

Navigate back to Hugging Face and make sure you are logged in. Select your user profile Settings -> Select Access Tokens. Alternatively, you can simply access this link while logged in to Hugging Face.

hugging face access token pt1
</p>
 

 

 

hugging face access token pt2

Create a new Access Token and name it whatever you want. Keep the role set to read. Once you create the new token, copy it to your clipboard.

hugging face access token pt3

 

Next we will use the access token in our Postman request to Hugging Face.

 

Milestone 5 - Make an authorized call to Hugging Face in Postman
Head back to your forked collection in Postman and navigate to the “Variables” tab on your collection.

Paste your Hugging Face access token in the “Current Value” column for the access_token variable. Be sure to hit Save! 

Only paste your token in “Current Value” (NOT Initial Value). Initial Value is shared with others that view or fork your collection, so saving to Current Value ensures that only you can see it! Access tokens should be treated like passwords and kept secret, otherwise others can call an API on your behalf.

 

The “hugging face summary” request already has Bearer Token type authorization saved on the request from when you forked it, as you can see in the Authorization tab of the request. You don’t have to change anything here but feel free to take a look. It will use the token you just pasted in the “Current Value” column of your collection variable access_token when sending the request!


hugging face access token in postman

 

Hover your mouse over {{accessToken}} and make sure Postman shows a Current Value for your token. If not, you probably forget to save your variable so go back and do that now!  


In your "hugging face summary" request, hit "Send" to call the facebook/bart-large-cnn model and see what comes back. You should see a summary of the long text from our collection variable “text”, similar to below. Awesome!



Are you getting 500 error with the facebook/bart-large-cnn model? Try a different summarization model from Hugging Face by replacing the model name in your path: list of summarization models on Hugging Face. ex: philschmid/bart-large-cnn-samsum


Next we’ll learn how Postman can help us make this API call with code in our app.

Milestone 6 - Generate code in Postman to call Hugging Face in our app
Now that your request to Hugging Face is working in Postman, we can generate code to use in our ‘summarizeText()’ function.

We’ll use Postman's client code generation feature to generate code that makes our API call using axios. axios is a library that makes it easy to make HTTP requests in NodeJS and the browser. We already added axios for you to this project in the package.json file to use in our server.

Next, in Postman make sure you have selected the “hugging face summary” request in your forked collection, then select the code icon (“</>”) on the far right side of the screen and select “NodeJs - Axios” from the dropdown menu.

We also want to use async/await in our request, which is a setting that we can enable in our code snippet! Calling an API using async/await provides a multitude of benefits; one being code readability, but also more importantly, it allows for better error handling by using try/catch blocks to handle exceptions. Click on the gear on the upper right corner to enable “Use async/await” to generate the appropriate snippet.

 



 

async/await codegen setting

Now that you’ve copied the “NodeJs - axios” code snippet from Postman, insert the code snippet from Postman in the line after the comment “INSERT CODE SNIPPET FROM POSTMAN BELOW” in summarize.js. We will edit the snippet in the next steps.

The snippet code creates a stringified JSON object named data, which contains the request body for the API call. (The JSON.stringify() step is actually not necessary because Axios stringifies JSON for you when you pass it a regular object, but we’ll use it anyway since Postman gave it to us)

It also defines a configuration object named “config” that contains the instructions for making the HTTP request to the Hugging Face API.

method: The HTTP method for the request. In this case, it is set to 'post' as we are making a POST request to the API.
url: The URL of the Hugging Face API endpoint for the "facebook/bart-large-cnn" model, which is used for text summarization.
headers: An object containing the headers for the request. We set the "Content-Type" header to "application/json" to indicate that we are sending JSON data in the request body.
data: The JSON data that we want to send as the request body. Here, we pass the data object, which contains the input text and the summarization parameters.
The actual API call is happening in the line const response = await axios.request(config);.  The await keyword pauses the execution of the script until the axios request is completed (with either a successful or failed response).

Using a try/catch block is a great way to handle errors appropriately. The snippet first “tries” the API call in the try block. If the request is successful, the response from the Hugging Face is saved to a variable called response and logged to the console. If the request fails, the error is logged to the console.

We’re not done yet! We need to make a few important changes to this snippet. Let’s tackle that in the next section.

 

Milestone 7 - Modify the code snippet for our app
The Postman snippet is very useful for getting the API call in our code quickly, but we will need  to edit a few things to make it fit into our app securely.


 

Modification 1: Mov/e the Axios import to the top of your file

The convention in JavaScript is to put all your imports at the top of the file. Cut and move the line below from inside your function to the very first line of your summarize.js file.

Snippet 3.3c)

```js
const axios = require('axios');
 ```


Modification 2: Change the static example text in the data object to “text”

The snippet is currently using the static example text from our Postman collection. Instead, we want to use the text variable passed into our function. Simply replace the long string “Amelia…” with the text variable (no quotes). It should look like below:

Snippet 3.3d)

```js

let data = JSON.stringify({
    "inputs": text,  // <-- use the text passed into the function
    "parameters": {
      "max_length": 100,
      "min_length": 30
    }
  });
```
Modification 3: Handle the response by returning data instead of logging

The snippet from Postman doesn’t know what you want to do with the response from the API, so it just logs it to the console. We need to instead return the data from our function so we can use it in our server response. 

The Postman snippet creates a function called makeRequest(), but since we are already inside our own function summarizeText(), let’s get rid of this second function and just keep the try/catch block that makes the actual API call.

Carefully delete the line async function makeRequest() {“ and the last  closing bracket “}” on the line before  makeRequest();. Also delete the line makeRequest();

Finally, instead of using console.log() to log the successful API response in the try block, let’s return the data from the API response.

Hint: Use the response example in Postman to look at what the response returns! We’re looking for the generated text. Hugging Face returns an array of objects that have a “summary_text” property. We simply want the summary_text property of the first object in this array, in other words: response.data[0].summary_text. Let’s return that instead of logging.

Replace the line:

```js
console.log(JSON.stringify(response.data));
```

With:


```js
return response.data[0].summary_text;
 ```

Before:
```js

async function makeRequest() {
  try {
   const response = await axios.request(config);
   console.log(JSON.stringify(response.data));
  }
  catch (error) {
   console.log(error);
  }
}

makeRequest();
 ```

After: 

Snippet 3.3e)

```js
try {
  const response = await axios.request(config);
  return response.data[0].summary_text;
} catch (err) {
  console.log(err);
}
```
 

Modification 4: Hide your access token as a secret in Replit!

The Postman snippet hard-codes your access token into the code. You don’t want this secret access token in your Replit code, because the whole world can see it!

Cut your Hugging Face access token from the snippet. You can find it after the “Bearer” keyword in the Authorization header, and it should start with “hf_….”.

We’re now going to use Replit’s Secrets feature to store this access token securely to then use in our code.

Navigate to the bottom left hand corner of Replit to the Tools dropdown. Then click on the Secrets icon to save your access token. Name your secret “ACCESS_TOKEN” and paste in your access token as the value.

Replit secrets part 1

Replit secrets pt2 

 

Once the access token is stored properly in Replit’s secrets, we can access it in our back end code with process.env[‘ACCESS_TOKEN’].

Update the “Authorization” property of the headers object in your pasted snippet to use your secret instead of a hard-coded access token. You can replace the existing line with this line (note: the single whitespace after ‘Bearer ‘ is important!):

Authorization': 'Bearer ' + process.env['ACCESS_TOKEN']
 

Your config object should now look like this:


config object

 

At this point, your full summarize.js file should look something like this. Make sure your code looks like this before continuing. The comments (“// …”) are here to help explain what’s happening in the code - don't worry if they aren't in your code.

Snippet 3.3f)

```js

// Axios is the framework we will be using to calling the API

const axios = require('axios');


// This is the function where the call to the API is made. Returns the summarized text as a string.
async function summarizeText(text) {
   // INSERT CODE SNIPPET FROM POSTMAN BELOW
  let data = JSON.stringify({
    "inputs": text,
    "parameters": {
      "max_length": 100,
      "min_length": 30
    }
  });

  // A config object that will contain the instructions for the API call

  let config = {
    method: 'post',
    url: 'https://api-inference.huggingface.co/models/facebook/bart-large-cnn',
    headers: {
      'Content-Type': 'application/json',
      'Authorization': 'Bearer ' + process.env['ACCESS_TOKEN']
    },
    data: data
  };

  // Capture the request in a try/catch to check for any errors that may occur

  try {
    const response = await axios.request(config);
    // Return the summary text from the response
    return response.data[0].summary_text;
  } catch (err) {
    console.log(err);
  }
}




// Allows for summarizeText() to be called outside of this file

module.exports = summarizeText;
 ```

Milestone 8 - Finish the POST /summarize endpoint
Whew! You’ve come so far. Now that you’ve coded the logic for making the API call to Hugging Face in the summarizeText() function, let’s use it in our server.

Return to index.js, which is our server code. Add this line of code to import your summarizeText function, on the line after where we are defining the port as 3000. 

Snippet 3.3g)

```js
const summarizeText = require('./summarize.js');
``` 

Finally, update your app.post(...) endpoint to use your summarizeText() function and send a response, like below

Snippet 3.3h)

```js
app.post('/summarize', (req, res) => {
 // get the text_to_summarize property from the request body
  const text = req.body.text_to_summarize;

 // call your summarizeText function, passing in the text from the request
  summarizeText(text) 
    .then(response => {
       res.send(response); // Send the summary text as a response to the client
    })
    .catch(error => {
      console.log(error.message);
    });
});
 ```


Your full server in the index.js file should now look like this:

Snippet 3.3i)
```js
const express = require('express');

const app = express();

const port = 3000;

const summarizeText = require('./summarize.js');

// Parse JSON bodies (as sent by API clients)
app.use(express.json());

app.use(express.static('public')); // Serve static files from the 'public' directory

// Handle POST requests to the '/summarize' endpoint
app.post('/summarize', (req, res) => {
 // get the text_to_summarize property from the request body
  const text = req.body.text_to_summarize;

  // call your summarizeText function, passing in the text from the request
  summarizeText(text)
    .then(response => {
      res.send(response); // Send the summary text as a response
    })
    .catch(error => {
      console.log(error.message);
    });
});

// Start the server
app.listen(port, () => {
  console.log('Server running at http://localhost:${port}/');
});
```

<h1><a href="https://verify.skilljar.com/c/enikaevtag3x">My Certification</a></h1>
<img src="https://github.com/user-attachments/assets/63c5f32c-5cf1-48cb-917d-03ce9b968eaa"/>

