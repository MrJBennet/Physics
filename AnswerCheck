<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document Analyzer & Feedback</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700&display=swap" rel="stylesheet">
    <style>
        body {
            font-family: 'Inter', sans-serif;
            background-color: #f3f4f6; /* Light gray background */
        }
        .container {
            max-width: 1000px;
            margin: 2rem auto;
            padding: 1.5rem;
            background-color: white;
            border-radius: 0.75rem; /* Rounded corners */
            box-shadow: 0 10px 15px -3px rgba(0, 0, 0, 0.1), 0 4px 6px -2px rgba(0, 0, 0, 0.05);
        }
        .btn {
            padding: 0.75rem 1.5rem;
            border-radius: 0.5rem; /* Rounded corners */
            font-weight: 600;
            transition: background-color 0.3s ease;
            cursor: pointer;
        }
        .btn-primary {
            background-color: #4f46e5; /* Indigo */
            color: white;
        }
        .btn-primary:hover {
            background-color: #4338ca;
        }
        .label {
            display: block;
            margin-bottom: 0.5rem;
            font-weight: 500;
            color: #374151; /* Darker gray text */
        }
        .input-field, .textarea-field {
            width: 100%;
            padding: 0.75rem;
            border: 1px solid #d1d5db; /* Gray border */
            border-radius: 0.5rem; /* Rounded corners */
            margin-bottom: 1rem;
            box-sizing: border-box;
        }
        .textarea-field {
            min-height: 100px; /* Adjusted height */
            resize: vertical;
        }
        .image-preview {
            max-width: 100%;
            max-height: 300px; /* Adjusted height */
            border-radius: 0.5rem;
            margin-top: 1rem;
            margin-bottom: 1rem;
            border: 1px solid #e5e7eb; /* Lighter gray border */
            object-fit: contain; /* Ensure image aspect ratio is maintained */
        }
        .results-box {
            margin-top: 1.5rem;
            padding: 1rem;
            background-color: #f9fafb; /* Very light gray */
            border: 1px solid #e5e7eb;
            border-radius: 0.5rem;
        }
        .results-box h3 {
            font-size: 1.125rem; /* text-lg */
            font-weight: 600;
            margin-bottom: 0.75rem;
            color: #1f2937; /* Even darker gray text */
        }
        .results-content {
            white-space: pre-wrap; /* Preserve whitespace and newlines */
            font-family: monospace;
            background-color: #ffffff;
            padding: 0.75rem;
            border-radius: 0.375rem;
            border: 1px solid #d1d5db;
            max-height: 300px; /* Adjusted height */
            overflow-y: auto;
        }
        .loader {
            border: 4px solid #f3f3f3;
            border-top: 4px solid #4f46e5;
            border-radius: 50%;
            width: 40px;
            height: 40px;
            animation: spin 1s linear infinite;
            margin: 2rem auto;
        }
        @keyframes spin {
            0% { transform: rotate(0deg); }
            100% { transform: rotate(360deg); }
        }
        .error-message-box {
            background-color: #fee2e2; /* Light red */
            color: #b91c1c; /* Dark red */
            padding: 1rem;
            border-radius: 0.5rem;
            margin-bottom: 1rem;
            border: 1px solid #fecaca; /* Red border */
        }
        .input-group-title {
            font-size: 1.25rem; /* text-xl */
            font-weight: 600;
            color: #111827; /* Darkest gray */
            margin-bottom: 1rem;
            padding-bottom: 0.5rem;
            border-bottom: 1px solid #e5e7eb;
        }
    </style>
</head>
<body>
    <div class="container">
        <header class="text-center mb-8">
            <h1 class="text-3xl font-bold text-gray-800">Document Analyzer & Feedback Tool</h1>
            <p class="text-gray-600 mt-2">Upload a scanned document, provide a model answer (via upload or text), and get AI-powered feedback.</p>
        </header>

        <div id="errorMessage" class="hidden"></div>

        <div class="grid md:grid-cols-2 gap-8">
            <div>
                <div class="mb-6">
                    <h2 class="input-group-title">Student's Document</h2>
                    <label for="scannedDocument" class="label">1. Upload Scanned Document (Image)</label>
                    <input type="file" id="scannedDocument" accept="image/*" class="input-field">
                    <img id="imagePreview" src="#" alt="Student Document Preview" class="hidden image-preview"/>
                </div>

                <div>
                    <h2 class="input-group-title">Model Answer</h2>
                    <label for="modelAnswerDocument" class="label">2. Upload Model Answer Document (Optional)</label>
                    <input type="file" id="modelAnswerDocument" accept="image/*" class="input-field">
                    <img id="modelAnswerImagePreview" src="#" alt="Model Answer Preview" class="hidden image-preview"/>
                    
                    <label for="modelAnswerText" class="label mt-4">Or Enter Model Answer Text</label>
                    <textarea id="modelAnswerText" class="textarea-field" placeholder="Type the model answer here if not uploading an image..."></textarea>
                </div>

                <button id="analyzeButton" class="btn btn-primary w-full mt-6">Analyze Document</button>
            </div>

            <div>
                <div id="loadingIndicator" class="hidden text-center">
                    <div class="loader"></div>
                    <p class="text-gray-600">Analyzing... This may take a moment.</p>
                </div>

                <div id="resultsSection" class="hidden">
                    <div class="results-box">
                        <h3>Extracted Text from Student's Document</h3>
                        <div id="extractedStudentText" class="results-content bg-gray-50"></div>
                    </div>

                    <div class="results-box mt-4">
                        <h3>Model Answer Text</h3>
                        <div id="extractedModelAnswerText" class="results-content bg-gray-50"></div>
                    </div>

                    <div class="results-box mt-4">
                        <h3>Feedback on Answer</h3>
                        <div id="feedback" class="results-content bg-gray-50"></div>
                    </div>
                </div>
            </div>
        </div>
    </div>

    <script>
        // DOM Elements
        const scannedDocumentInput = document.getElementById('scannedDocument');
        const imagePreview = document.getElementById('imagePreview');
        
        const modelAnswerDocumentInput = document.getElementById('modelAnswerDocument');
        const modelAnswerImagePreview = document.getElementById('modelAnswerImagePreview');
        const modelAnswerTextarea = document.getElementById('modelAnswerText');

        const analyzeButton = document.getElementById('analyzeButton');
        const loadingIndicator = document.getElementById('loadingIndicator');
        const resultsSection = document.getElementById('resultsSection');
        const extractedStudentTextDiv = document.getElementById('extractedStudentText');
        const extractedModelAnswerTextDiv = document.getElementById('extractedModelAnswerText');
        const feedbackDiv = document.getElementById('feedback');
        const errorMessageDiv = document.getElementById('errorMessage');

        let base64StudentImageData = null;
        let base64ModelAnswerImageData = null;

        // --- Configuration for Gemini API ---
        const apiKey = ""; // Canvas will provide this if empty
        const textGenModel = "gemini-2.0-flash"; 

        // --- Event Listeners ---
        scannedDocumentInput.addEventListener('change', (event) => handleImageUpload(event, 'student'));
        modelAnswerDocumentInput.addEventListener('change', (event) => handleImageUpload(event, 'modelAnswer'));
        analyzeButton.addEventListener('click', analyzeDocument);

        // --- Functions ---

        function showErrorMessage(message) {
            errorMessageDiv.innerHTML = `<div class="error-message-box">${message}</div>`;
            errorMessageDiv.classList.remove('hidden');
        }

        function clearErrorMessage() {
            errorMessageDiv.innerHTML = '';
            errorMessageDiv.classList.add('hidden');
        }

        function handleImageUpload(event, type) {
            clearErrorMessage();
            const file = event.target.files[0];
            let targetPreview;
            
            if (type === 'student') {
                targetPreview = imagePreview;
            } else if (type === 'modelAnswer') {
                targetPreview = modelAnswerImagePreview;
            }

            if (file) {
                if (!file.type.startsWith('image/')) {
                    showErrorMessage('Please upload a valid image file (e.g., JPG, PNG, GIF).');
                    event.target.value = ''; // Reset file input
                    targetPreview.classList.add('hidden');
                    if (type === 'student') base64StudentImageData = null;
                    if (type === 'modelAnswer') base64ModelAnswerImageData = null;
                    return;
                }

                const reader = new FileReader();
                reader.onload = (e) => {
                    const base64Data = e.target.result.split(',')[1]; // Get base64 part
                    if (type === 'student') {
                        base64StudentImageData = base64Data;
                    } else if (type === 'modelAnswer') {
                        base64ModelAnswerImageData = base64Data;
                    }
                    targetPreview.src = e.target.result;
                    targetPreview.classList.remove('hidden');
                }
                reader.onerror = () => {
                    showErrorMessage('Error reading the image file.');
                    if (type === 'student') base64StudentImageData = null;
                    if (type === 'modelAnswer') base64ModelAnswerImageData = null;
                    targetPreview.classList.add('hidden');
                }
                reader.readAsDataURL(file);
            } else {
                if (type === 'student') base64StudentImageData = null;
                if (type === 'modelAnswer') base64ModelAnswerImageData = null;
                targetPreview.classList.add('hidden');
            }
        }

        async function analyzeDocument() {
            clearErrorMessage();
            // Validate student document
            if (!base64StudentImageData) {
                showErrorMessage('Please upload the student\'s scanned document image.');
                return;
            }

            // Determine model answer source
            let modelAnswerSourceIsImage = !!base64ModelAnswerImageData;
            let modelAnswerText = modelAnswerTextarea.value.trim();

            if (!modelAnswerSourceIsImage && !modelAnswerText) {
                showErrorMessage('Please either upload a model answer document or type the model answer text.');
                return;
            }

            loadingIndicator.classList.remove('hidden');
            resultsSection.classList.add('hidden');
            extractedStudentTextDiv.textContent = '';
            extractedModelAnswerTextDiv.textContent = '';
            feedbackDiv.textContent = '';
            analyzeButton.disabled = true;

            try {
                // Step 1: Extract text from student's image
                const studentImagePrompt = "Extract all text, including handwritten text, from this image. Present the text as a single block. If no text is found, state 'No text detected'.";
                const extractedStudentText = await callGeminiForImageUnderstanding(studentImagePrompt, base64StudentImageData);
                extractedStudentTextDiv.textContent = extractedStudentText || "No text could be extracted from the student's document.";

                if (!extractedStudentText || extractedStudentText.toLowerCase() === "no text detected") {
                    extractedModelAnswerTextDiv.textContent = "N/A (Student document has no text)";
                    feedbackDiv.textContent = "Cannot provide feedback as no text was extracted from the student's document.";
                    resultsSection.classList.remove('hidden');
                    loadingIndicator.classList.add('hidden');
                    analyzeButton.disabled = false;
                    return;
                }

                // Step 2: Determine and extract/get model answer text
                let finalModelAnswerText = "";
                if (modelAnswerSourceIsImage) {
                    const modelAnswerImagePrompt = "Extract all text, including handwritten text, from this image (this is a model answer). Present the text as a single block. If no text is found, state 'No text detected'.";
                    finalModelAnswerText = await callGeminiForImageUnderstanding(modelAnswerImagePrompt, base64ModelAnswerImageData);
                    if (!finalModelAnswerText || finalModelAnswerText.toLowerCase() === "no text detected") {
                        extractedModelAnswerTextDiv.textContent = "No text could be extracted from the uploaded model answer image. Please check the image or type the model answer.";
                        feedbackDiv.textContent = "Cannot provide feedback as model answer text could not be obtained from the image.";
                        resultsSection.classList.remove('hidden');
                        loadingIndicator.classList.add('hidden');
                        analyzeButton.disabled = false;
                        return;
                    }
                    extractedModelAnswerTextDiv.textContent = finalModelAnswerText;
                } else {
                    finalModelAnswerText = modelAnswerText;
                    extractedModelAnswerTextDiv.textContent = finalModelAnswerText; // Display typed text
                }
                
                // Step 3: Get feedback by comparing extracted student text with the final model answer text
                const feedbackPrompt = `You are an AI assistant for evaluating student answers.
The student's answer, extracted from their document, is:
---
${extractedStudentText}
---
The model answer is:
---
${finalModelAnswerText}
---
Compare the student's answer with the model answer. Provide constructive feedback on the student's answer.
Focus on:
1.  Accuracy and completeness compared to the model answer.
2.  Key points missed by the student.
3.  Areas where the student's answer aligns well with the model.
4.  Any inaccuracies or misunderstandings in the student's answer.
Present the feedback in a clear, structured manner. Use bullet points or numbered lists where appropriate for readability.`;

                const feedback = await callGeminiForTextGeneration(feedbackPrompt);
                feedbackDiv.textContent = feedback || "Could not generate feedback at this time.";

                resultsSection.classList.remove('hidden');

            } catch (error) {
                console.error('Error during analysis:', error);
                showErrorMessage(`An error occurred: ${error.message}. Check the console for more details.`);
                resultsSection.classList.add('hidden'); // Ensure results are hidden on error
            } finally {
                loadingIndicator.classList.add('hidden');
                analyzeButton.disabled = false;
            }
        }

        // --- Gemini API Call Helper Functions ---
        async function callGeminiForImageUnderstanding(prompt, imageDataBase64) {
            const apiUrl = `https://generativelanguage.googleapis.com/v1beta/models/${textGenModel}:generateContent?key=${apiKey}`;
            const payload = {
                contents: [{ parts: [{ text: prompt }, { inlineData: { mimeType: "image/png", data: imageDataBase64 } }] }],
                generationConfig: { /* temperature: 0.7, maxOutputTokens: 2048 */ }
            };

            try {
                const response = await fetch(apiUrl, {
                    method: 'POST',
                    headers: { 'Content-Type': 'application/json' },
                    body: JSON.stringify(payload)
                });
                if (!response.ok) {
                    const errorBody = await response.json();
                    console.error("API Error (Image Understanding):", errorBody);
                    throw new Error(`API request failed (${response.status}): ${errorBody.error?.message || response.statusText}`);
                }
                const result = await response.json();
                if (result.candidates && result.candidates[0]?.content?.parts?.[0]?.text) {
                    return result.candidates[0].content.parts[0].text;
                } else if (result.promptFeedback?.blockReason) {
                     throw new Error(`Content blocked by API: ${result.promptFeedback.blockReason} - ${result.promptFeedback.blockReasonMessage || ''}`);
                }
                console.warn("Unexpected API response (Image Understanding):", result);
                return "Could not extract text (unexpected API response).";
            } catch (error) {
                console.error('Error calling Gemini API for image understanding:', error);
                throw error;
            }
        }

        async function callGeminiForTextGeneration(prompt) {
            const apiUrl = `https://generativelanguage.googleapis.com/v1beta/models/${textGenModel}:generateContent?key=${apiKey}`;
            const payload = {
                contents: [{ role: "user", parts: [{ text: prompt }] }],
                generationConfig: { /* temperature: 0.7, maxOutputTokens: 2048 */ }
            };
            try {
                const response = await fetch(apiUrl, {
                    method: 'POST',
                    headers: { 'Content-Type': 'application/json' },
                    body: JSON.stringify(payload)
                });
                if (!response.ok) {
                    const errorBody = await response.json();
                    console.error("API Error (Text Generation):", errorBody);
                    throw new Error(`API request failed (${response.status}): ${errorBody.error?.message || response.statusText}`);
                }
                const result = await response.json();
                if (result.candidates && result.candidates[0]?.content?.parts?.[0]?.text) {
                    return result.candidates[0].content.parts[0].text;
                } else if (result.promptFeedback?.blockReason) {
                     throw new Error(`Content blocked by API: ${result.promptFeedback.blockReason} - ${result.promptFeedback.blockReasonMessage || ''}`);
                }
                console.warn("Unexpected API response (Text Generation):", result);
                return "Could not generate feedback (unexpected API response).";
            } catch (error) {
                console.error('Error calling Gemini API for text generation:', error);
                throw error;
            }
        }
    </script>
</body>
</html>
