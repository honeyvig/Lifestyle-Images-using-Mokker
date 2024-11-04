# Lifestyle-Images-using-Mokker
Creating a tool that generates company-specific interview prep questions involves a combination of web scraping (or using an API) and building a simple frontend interface. Below, I'll provide a Python script that sets up the backend for generating interview questions using either web scraping or the OpenAI API.
Project Overview

Key Responsibilities:

    Develop a Python script for generating interview questions.
    Integrate web scraping techniques or OpenAI API for data retrieval.
    Ensure the output is in text format, with an option for podcast-style delivery.

Step 1: Install Required Libraries

First, you'll need to install some libraries. For web scraping, we'll use BeautifulSoup and requests, and for OpenAI integration, we'll use the openai library.

bash

pip install beautifulsoup4 requests openai Flask

Step 2: Set Up the Backend

Here's a sample Python script that sets up a Flask server to handle the request for generating interview questions.

python

from flask import Flask, request, jsonify
import requests
from bs4 import BeautifulSoup
import openai

app = Flask(__name__)

# OpenAI API key
openai.api_key = 'your_openai_api_key'

def scrape_questions(company_name):
    # This function is a placeholder for web scraping logic.
    # Modify the URL and scraping logic based on your needs.
    url = f'https://www.example.com/interview-questions/{company_name}'  # Replace with a valid URL
    response = requests.get(url)
    soup = BeautifulSoup(response.content, 'html.parser')
    
    questions = []
    for item in soup.find_all('div', class_='question'):  # Modify based on the actual HTML structure
        questions.append(item.get_text())
    
    return questions

def generate_questions_with_openai(company_name):
    prompt = f"Generate 50 interview questions for {company_name}:"
    response = openai.ChatCompletion.create(
        model='gpt-3.5-turbo',
        messages=[{"role": "user", "content": prompt}]
    )
    
    return response['choices'][0]['message']['content'].splitlines()

@app.route('/generate-questions', methods=['POST'])
def generate_questions():
    data = request.json
    company_name = data.get('company_name')
    method = data.get('method')  # 'scrape' or 'openai'

    if method == 'scrape':
        questions = scrape_questions(company_name)
    elif method == 'openai':
        questions = generate_questions_with_openai(company_name)
    else:
        return jsonify({"error": "Invalid method selected."}), 400

    return jsonify({"questions": questions})

if __name__ == "__main__":
    app.run(debug=True)

Explanation of the Code

    Flask Setup: This script sets up a basic Flask server that listens for POST requests to the /generate-questions endpoint.
    Web Scraping Function: The scrape_questions function demonstrates how you might scrape questions from a website. You will need to customize the URL and HTML parsing logic based on the actual site structure.
    OpenAI Function: The generate_questions_with_openai function sends a prompt to the OpenAI API to generate interview questions for the specified company.
    API Endpoint: The /generate-questions endpoint accepts a JSON payload with company_name and method to specify whether to scrape or use OpenAI.

Step 3: Frontend Interface (Optional)

For a user-friendly interface, you could use HTML/JavaScript with a simple form to allow users to select a company and the method of question generation.

html

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Interview Prep Tool</title>
</head>
<body>
    <h1>Interview Preparation Tool</h1>
    <form id="question-form">
        <input type="text" id="company" placeholder="Enter Company Name" required>
        <select id="method">
            <option value="scrape">Scrape</option>
            <option value="openai">OpenAI</option>
        </select>
        <button type="submit">Generate Questions</button>
    </form>
    <div id="questions"></div>

    <script>
        document.getElementById('question-form').onsubmit = async function(e) {
            e.preventDefault();
            const company = document.getElementById('company').value;
            const method = document.getElementById('method').value;
            
            const response = await fetch('/generate-questions', {
                method: 'POST',
                headers: {
                    'Content-Type': 'application/json',
                },
                body: JSON.stringify({ company_name: company, method: method }),
            });

            const data = await response.json();
            document.getElementById('questions').innerText = data.questions.join('\n');
        };
    </script>
</body>
</html>

Final Notes

    Security: Ensure you handle sensitive information, such as your OpenAI API key, securely.
    Scraping Policy: Be aware of the scraping policy of the sites you intend to scrape from; respect robots.txt and terms of service.
    Deployment: Once you have tested locally, consider deploying your Flask application using platforms like Heroku, AWS, or DigitalOcean.
