Part 1: Introduction to Flask

Flask is a tool that helps us build websites using Python. It's a micro framework, which means it's small and simple to use, making it perfect for beginners and small projects. With Flask, we can create web pages, handle user input, and make our websites interactive without getting overwhelmed by too many complicated features.

Flask uses Jinja2 as its templating engine, allowing you to separate your HTML from your Python code.
The implementation of templates with Jinja allows us to create dynamic web pages easily. Jinja is a tool that works with Flask to help us design HTML templates that can automatically update with different data. Instead of writing the same HTML code repeatedly, we use Jinja to insert data into our web pages, making them more efficient and easier to manage. This way, we can create interactive and personalized websites quickly.


Setting Up the Environment

Install Flask: 
Open the command prompt or terminal and type pip install flask.
Create a new folder for your project (e.g., 'ContactForm')

Creating a Basic Flask Application:
Open your code editor and create a new file called app.py inside your project folder.
Write the following code to create a basic Flask application:

'''python

# app.py
from flask import Flask

app = Flask(__name__)

@app.route('/')
def home():
    return "Hello, World!"

if __name__ == '__main__':
    app.run(debug=True)
'''




Running the Flask Application
In the command prompt or terminal, navigate to your project folder and run python app.py.
Open a web browser and go to http://127.0.0.1:5000/. You should see "Hello, World!".

Part 2: Creating the Contact Form
Objective:
Create an HTML form and display it using Flask.
Steps:
Creating the Form Template
Inside your project folder, create a new folder called templates.
In the templates folder, create a new file called contactform.html.
Design a simple HTML form with fields: first name, last name, email, country, message, gender (radio buttons), and subject (checkboxes).

'''html
<!-- templates/contact.html -->
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Contact Form</title>
</head>
<body>
    <form method="POST" action="/submit">
        <label for="firstname">First Name:</label>
        <input type="text" id="firstname" name="firstname" required><br>

        <label for="lastname">Last Name:</label>
        <input type="text" id="lastname" name="lastname" required><br>

        <label for="email">Email:</label>
        <input type="email" id="email" name="email" required><br>

        <label for="country">Country:</label>
        <select id="country" name="country" required>
            <option value="USA">USA</option>
            <option value="Canada">Canada</option>
            <option value="UK">UK</option>
        </select><br>

        <label for="message">Message:</label>
        <textarea id="message" name="message" required></textarea><br>

        <label>Gender:</label>
        <input type="radio" id="male" name="gender" value="M" required>
        <label for="male">Male</label>
        <input type="radio" id="female" name="gender" value="F" required>
        <label for="female">Female</label><br>

        <label>Subject:</label>
        <input type="checkbox" id="repair" name="subject" value="Repair">
        <label for="repair">Repair</label>
        <input type="checkbox" id="order" name="subject" value="Order">
        <label for="order">Order</label>
        <input type="checkbox" id="others" name="subject" value="Others" checked>
        <label for="others">Others</label><br>

        <button type="submit">Submit</button>
    </form>
</body>
</html>
'''

Rendering the Form with Flask
Modify app.py to render the contact.html template when accessing the /contact route.

'''python
# app.py (continued)
from flask import Flask, render_template

app = Flask(__name__)

@app.route('/')
def home():
    return "Hello, World!"

@app.route('/contact')
def contact():
    return render_template('contact.html')

if __name__ == '__main__':
    app.run(debug=True)
'''

Running the Application
Restart your Flask application (stop it by pressing Ctrl+C and then run python app.py again).
Open a web browser and go to http://127.0.0.1:5000/contact. You should see your contact form.
Part 3: Handling Form Submission
Objective:
Process the form submission, validate the data, and provide feedback to the user.
Steps:
Handling POST Requests

Create a route to handle form submissions (/submit).
Use the request object to access form data.
Basic Validation

Check if all required fields are filled.
Validate the email format (basic check).
Providing Feedback

If validation fails, display an error message.
If validation succeeds, display a thank you message with the submitted data.

'''python
# app.py (continued)
from flask import Flask, render_template, request

app = Flask(__name__)

@app.route('/')
def home():
    return "Hello, World!"

@app.route('/contact')
def contact():
    return render_template('contact.html')

@app.route('/submit', methods=['POST'])
def submit():
    # Retrieve form data
    firstname = request.form['firstname']
    lastname = request.form['lastname']
    email = request.form['email']
    country = request.form['country']
    message = request.form['message']
    gender = request.form['gender']
    subject = request.form.getlist('subject')

    # Simple email validation
    if '@' not in email or '.' not in email:
        return "Invalid email address. Please go back and correct it."

    # If all validations pass
    return render_template('thankyou.html', firstname=firstname, lastname=lastname, email=email, country=country, message=message, gender=gender, subject=subject)

if __name__ == '__main__':
    app.run(debug=True)
'''

thankyou.html Template:

'''html
<!-- templates/thankyou.html -->
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Thank You</title>
</head>
<body>
    <h1>Thank you for contacting us!</h1>
    <p>Here is the information you submitted:</p>
    <ul>
        <li>First Name: {{ firstname }}</li>
        <li>Last Name: {{ lastname }}</li>
        <li>Email: {{ email }}</li>
        <li>Country: {{ country }}</li>
        <li>Message: {{ message }}</li>
        <li>Gender: {{ gender }}</li>
        <li>Subject: {{ subject }}</li>
    </ul>
</body>
</html>
'''

Running the Application
Restart your Flask application (stop it by pressing Ctrl+C and then run python app.py again).
Open a web browser and go to http://127.0.0.1:5000/contact. Fill out the form and submit it to see the thank you message.

Part 4: Implementing Honeypot Anti-Spam Technique
Objective:
Implement a honeypot field to help prevent spam submissions.

Steps:
Adding a Honeypot Field

Add an invisible honeypot field to the form (contact.html).
Checking the Honeypot Field

Modify the form submission route to check if the honeypot field is filled.
If the honeypot field is filled, reject the submission as spam.


'''html
<!-- templates/contact.html (continued) -->
<!-- Add this inside the form -->
<input type="text" name="honeypot" style="display:none">
'''


'''python
# app.py (continued)
@app.route('/submit', methods=['POST'])
def submit():
    # Retrieve form data
    honeypot = request.form.get('honeypot')
    if honeypot:
        # If honeypot is filled, reject as spam
        return "Spam detected. Please go back and try again."

    # Existing form data retrieval and validation code here
    firstname = request.form['firstname']
    lastname = request.form['lastname']
    email = request.form['email']
    country = request.form['country']
    message = request.form['message']
    gender = request.form['gender']
    subject = request.form.getlist('subject')

    # Simple email validation
    if '@' not in email or '.' not in email:
        return "Invalid email address. Please go back and correct it."

    # If all validations pass
    return render_template('thankyou.html', firstname=firstname, lastname=lastname, email=email, country=country, message=message, gender=gender, subject=subject)

if __name__ == '__main__':
    app.run(debug=True)
'''






##### Final note :
  
  There is 3 main components here (from creation, validation and feedback) and 3 concept we had to learn (importance of checking user input, using Flask to handle web forms and basic security measures like honeypot).

More insights about :

Sanitization : cleaning the data people enter into a form to remove any harmful or unwanted parts, like script tags that could be used for hacking.

Validation : checking that the data is correct and complete, like making sure an email address has the right format or that all required fields are filled in.

Sanitization and validation of a form are important steps to keep our websites safe and working correctly.
Together, these steps help protect our site and ensure it gets the right information.


POST vs. GET Requests
When you visit a website, your computer talks to another computer (called a server) to get the information you need. There are two main ways this happens: with POST requests and GET requests.

GET Request: Imagine you are asking your teacher for a book. You just say, "Can I have the math book?" The teacher gives you the book. This is like a GET request, where your computer asks for information and gets it.

POST Request: Now, imagine you are handing in your homework. You give your homework to the teacher, and the teacher checks it. This is like a POST request, where your computer sends information (like a form you filled out) to the server.

The implementation of POST and GET methods is how we send and receive information on websites. The GET method is used to request data from a server, like when you visit a web page and see its content. The POST method is used to send data to the server, like when you fill out a form and submit it. By using these methods, our websites can interact with users and handle their requests and submissions effectively.

Protecting Against XSS Vulnerabilities
XSS (Cross-Site Scripting) is when a bad person tries to sneak bad code into a website to make it do things it shouldn't. To protect against this, we need to clean up any information people type into our website. It's like making sure no one puts dangerous things in a sandbox where we play.

Protecting Against SSTI Attacks
SSTI (Server-Side Template Injection) is when a bad person tries to trick the server into running harmful commands. To stop this, we need to be very careful about how we handle and show information on our website. It's like making sure no one hides something bad in a surprise box.

Honeypots as a security measures
Basic security measures like the honeypot technique help protect our websites from spam and malicious users. A honeypot is a hidden field in a form that regular users don't see and shouldn't fill out. If a spam bot fills in this hidden field, we know it's not a genuine user and can block the submission. This simple trick helps keep our website safe from automated spam attacks.

Using a Micro Framework
A micro framework like Flask is a small, easy-to-use toolkit that helps us build websites quickly. Think of it like a small set of Lego blocks that lets us build our projects easily without too many complicated pieces.

Performing a Deployment
Deploying is when we take our website from our computer and put it on the internet so everyone can see it. It's like finishing a cool drawing and then putting it up on the school bulletin board for everyone to admire