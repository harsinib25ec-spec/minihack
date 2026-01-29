<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>AI Automated Essay Grader</title>
    <style>
        body {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            background-color: #f4f4f9;
            display: flex;
            justify-content: center;
            align-items: center;
            min-height: 100vh;
            margin: 0;
        }
        .container {
            background: white;
            padding: 2rem;
            border-radius: 12px;
            box-shadow: 0 4px 20px rgba(0,0,0,0.1);
            width: 100%;
            max-width: 600px;
        }
        h1 {
            text-align: center;
            color: #333;
            margin-bottom: 1.5rem;
        }
        textarea {
            width: 100%;
            height: 200px;
            padding: 15px;
            border: 2px solid #ddd;
            border-radius: 8px;
            resize: vertical;
            font-size: 16px;
            box-sizing: border-box; /* Ensures padding doesn't affect width */
        }
        textarea:focus {
            outline: none;
            border-color: #4a90e2;
        }
        button {
            width: 100%;
            padding: 15px;
            margin-top: 15px;
            background-color: #4a90e2;
            color: white;
            border: none;
            border-radius: 8px;
            font-size: 18px;
            cursor: pointer;
            transition: background 0.3s;
        }
        button:hover {
            background-color: #357abd;
        }
        #result {
            margin-top: 20px;
            text-align: center;
            font-size: 24px;
            font-weight: bold;
            color: #333;
            display: none;
            padding: 20px;
            border-radius: 8px;
        }
        .loading {
            color: #666;
            font-size: 16px;
            font-weight: normal;
        }
    </style>
</head>
<body>

<div class="container">
    <h1>AI Essay Grader</h1>
    <p style="text-align:center; color:#666;">Type your essay below to get an instant AI score.</p>
    
    <textarea id="essayInput" placeholder="Write your essay here..."></textarea>
    
    <button onclick="gradeEssay()">Grade My Essay</button>
    
    <div id="result"></div>
</div>

<script>
    async function gradeEssay() {
        const text = document.getElementById('essayInput').value;
        const resultDiv = document.getElementById('result');

        if (!text.trim()) {
            alert("Please write something first!");
            return;
        }

        // Show loading state
        resultDiv.style.display = 'block';
        resultDiv.innerHTML = '<span class="loading">Analyzing text structure and semantics...</span>';
        resultDiv.style.backgroundColor = '#f4f4f9';

        try {
            const response = await fetch('/predict', {
                method: 'POST',
                headers: {
                    'Content-Type': 'application/json'
                },
                body: JSON.stringify({ essay: text })
            });

            const data = await response.json();

            if (data.status === 'success') {
                let color = '#e8f5e9'; // Light Green
                let textColor = '#2e7d32'; // Dark Green
                
                // Change color based on score
                if(data.score < 5) {
                    color = '#ffebee'; // Red
                    textColor = '#c62828';
                } else if (data.score < 8) {
                    color = '#fff3e0'; // Orange
                    textColor = '#ef6c00';
                }

                resultDiv.style.backgroundColor = color;
                resultDiv.style.color = textColor;
                resultDiv.innerHTML = `Predicted Grade: ${data.score} / 10`;
            } else {
                resultDiv.innerHTML = "Error processing essay.";
            }

        } catch (error) {
            console.error('Error:', error);
            resultDiv.innerHTML = "Failed to connect to server.";
        }
    }
</script>

</body>
</html>