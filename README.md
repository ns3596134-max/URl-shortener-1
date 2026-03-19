# URl-shortener-1
from flask import Flask, request, redirect, render_template_string
import string, random

app = Flask(__name__)

# In-memory database (dictionary)
url_db = {}

# Function to generate short code
def generate_short_code(length=6):
    characters = string.ascii_letters + string.digits
    return ''.join(random.choice(characters) for _ in range(length))

# Home page
@app.route('/', methods=['GET', 'POST'])
def home():
    short_url = None
    if request.method == 'POST':
        long_url = request.form['url']
        code = generate_short_code()
        url_db[code] = long_url
        short_url = request.host_url + code

    return render_template_string('''
        <h2>URL Shortener</h2>
        <form method="post">
            <input type="text" name="url" placeholder="Enter URL" required>
            <button type="submit">Shorten</button>
        </form>
        {% if short_url %}
            <p>Short URL: <a href="{{ short_url }}">{{ short_url }}</a></p>
        {% endif %}
    ''', short_url=short_url)

# Redirect route
@app.route('/<code>')
def redirect_url(code):
    if code in url_db:
        return redirect(url_db[code])
    return "URL not found!"

if __name__ == '__main__':
    app.run(debug=True)
    
