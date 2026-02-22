# HMM Baum-Welch Visualizer

## Author
**Name:** Aakash P D  
**University Register Number:** TCR24CS001  

## Project Description
This project implements the **Hidden Markov Model (HMM)** using the **Baum-Welch algorithm**.  
It allows the user to:

- Input an **observed sequence** and the **number of hidden states**.  
- Compute **transition matrix (A)**, **emission matrix (B)**, **initial state distribution (π)**, and **probability of observation P(O|λ)**.  
- Visualize the **state transition diagram** using Graphviz.  

**Note:** Currently, Baum-Welch uses **randomized matrices** as a placeholder. This can be replaced with the full algorithm.

---

## How to Run

1. Clone or download the repository.  

2. Install Python dependencies:


```python
pip install flask numpy graphviz
```
Make sure Graphviz is installed and added to the system PATH.

Run the Flask app:

```python
python app.py
```
Open your browser and go to:

http://127.0.0.1:5000

Enter Observed Sequence (comma-separated) and Number of Hidden States, then click Run.

---

## File Structure
```
hmm_baum_weich_visual/
├─ app.py                  # Flask app main file
├─ my_hmm.py               # Baum-Welch algorithm module
├─ diagram.py              # Graphviz state diagram generator
├─ README.md               # Project description and instructions
├─ templates/              # Folder for HTML templates
│   └─ index.html          # Main webpage for inputs and outputs
└─ static/                 # Folder for CSS, JS, and images
    ├─ style.css           # Stylesheet for webpage
    ├─ script.js           # Optional JavaScript for interactivity
    └─ state_diagram.png   # Generated HMM state diagram (output)
```
---

 # Features

Input validation (no empty fields, hidden states must be an integer)

Shows matrices A, B, π and P(O|λ)

Generates state transition diagram (state_diagram.png) in static/ folder

Ready for future iterations visualization and advanced UI enhancements


---
# CODE 
### app.py

```python
from flask import Flask, render_template, request
import my_hmm as hmm
import diagram

app = Flask(__name__)

@app.route("/", methods=["GET", "POST"])
def home():
    if request.method == "POST":
        obs_seq = request.form.get("obs", "").strip()
        hidden_input = request.form.get("hidden", "").strip()

        if not obs_seq or not hidden_input:
            return render_template("index.html", error="Please fill in all fields")

        try:
            hidden_states = int(hidden_input)
        except ValueError:
            return render_template("index.html", error="Hidden states must be an integer")

        A, B, pi, P_O = hmm.baum_welch(obs_seq, hidden_states)
        diagram.create_diagram(A)

        return render_template("index.html", A=A, B=B, pi=pi, P_O=P_O)

    return render_template("index.html")
    

if __name__ == "__main__":
    app.run(debug=True)
```
### my_hmm.py
```python

import numpy as np

def baum_welch(obs_seq, N):
    O = list(map(int, obs_seq.split(",")))

    A = np.round(np.random.rand(N, N), 2)
    A = A / A.sum(axis=1)[:, None]

    B = np.round(np.random.rand(N, max(O)+1), 2)
    B = B / B.sum(axis=1)[:, None]

    pi = np.round(np.random.rand(N), 2)
    pi = pi / pi.sum()

    P_O = round(np.random.rand(), 4)

    return A.tolist(), B.tolist(), pi.tolist(), P_O
```
### diagram.py
```python from graphviz import Digraph

def create_diagram(A):
    dot = Digraph()
    N = len(A)

    for i in range(N):
        dot.node(f"S{i}", f"State {i}")

    for i in range(N):
        for j in range(N):
            dot.edge(f"S{i}", f"S{j}", label=str(A[i][j]))

    dot.render("static/state_diagram", format="png", cleanup=True)
```
### templates/index.html
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>HMM Baum-Welch Visualizer</title>
    <link rel="stylesheet" href="/static/style.css">
</head>
<body>
    <h1>HMM Baum-Welch Visualizer</h1>
    
    <form method="post">
        {% if error %}
        <p style="color:red;">{{ error }}</p>
        {% endif %}

        Observed Sequence (comma-separated): 
        <input type="text" name="obs" placeholder="0,1,0,2"><br><br>
        Number of Hidden States: 
        <input type="number" name="hidden" placeholder="2"><br><br>
        <input type="submit" value="Run">
    </form>

    {% if A %}
    <h3>Transition Matrix A</h3>
    <pre>{{ A }}</pre>
    <h3>Emission Matrix B</h3>
    <pre>{{ B }}</pre>
    <h3>Initial Distribution π</h3>
    <pre>{{ pi }}</pre>
    <h3>Probability P(O|λ)</h3>
    <pre>{{ P_O }}</pre>

    <h3>State Transition Diagram</h3>
    <img src="/static/state_diagram.png" alt="State Diagram">
    {% endif %}

    <script src="/static/script.js"></script>
</body>
</html>
```

### static/style.css
``` css
body {
    font-family: Arial, sans-serif;
    margin: 20px;
    background-color: #f8f9fa;
}

h1 {
    color: #2c3e50;
}

form input[type="text"], form input[type="number"] {
    padding: 5px;
    margin: 5px 0;
}

form input[type="submit"] {
    padding: 5px 15px;
    background-color: #3498db;
    color: white;
    border: none;
    cursor: pointer;
}
```
### static/script.js
```javascript
console.log("HMM Visualizer Loaded!");
```
