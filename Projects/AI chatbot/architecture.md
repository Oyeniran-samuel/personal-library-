Because we properly separated our code into the GrokAIProvider class, if we ever get the xAI API license in the future, we can simply write a new GrokAPIProvider class that uses HTTPS requests. We don't have to rewrite the Service layer or the Frontend. We just swap out the connector.

---

## 3. Environment Configuration: The ENV Security Protocol

Environmental variables are the standard way to handle secrets in modern software development. Here is the deep logic behind them.

Why we cannot hardcode secrets:
If you hardcode an API key into your .ts file (e.g., const apiKey = "sk-1234"), and you commit that file to a public GitHub repository, anyone in the world can see that key. They can use your credits, steal your data, or compromise your system. Hardcoding is strictly forbidden in professional environments.

The `.env` file:
The .env file is stored locally on the machine running the application. It holds key-value pairs (e.g., AI_PROVIDER=grok, PORT=4000). It is written into the system's environment variables when the app starts up, using a library called dotenv. Our backend reads this file, but it never exposes the values to the logs or the frontend.

The `.gitignore` file:
This is a special text file that tells Git exactly which files to ignore and never upload to GitHub. Inside our .gitignore, we have the line .env. This guarantees that even if we type git add ., the .env file stays safely on our computer and does not go to the cloud.

The `.env.example` file:
Because we don't upload the .env file to GitHub, a new developer cloning our code wouldn't know what secrets they need to create. To solve this, we create a .env.example file. It contains the variable NAMES, but with dummy values (e.g., AI_PROVIDER=your_provider_here). The developer copies this file, renames it to .env, and fills in their actual values.

---

## 4. Deployment Issues: The Free Tier Bottleneck

Deploying a local AI bot is notoriously difficult because local AI requires heavy RAM and CPU. Here is the step-by-step breakdown of the issues I faced.

The Netlify Roadblock:
Netlify is designed for static sites (HTML/CSS/React). They offer "Serverless Functions" for backend logic, but these functions have a hard execution timeout of 10 seconds. Because our local Grok engine takes longer than 10 seconds to generate a full response, Netlify will kill the process and return a 502 Bad Gateway error before the AI finishes its thought. Therefore, Netlify cannot host this backend.

The Render "Cold Start" Issue:
We moved the backend to Render, which is designed for persistent Node.js apps. However, we are using the Free Tier. 
Render's free servers follow a rule: "If no one touches the server for 15 minutes, we turn it off to save power."
When a user sends a message to a sleeping server, Render must turn the machine back on, load the operating system, load Node.js, and then load the heavy ACP/Grok engine. This process takes approximately 30 to 50 seconds.

The Frontend Timeout:
Web browsers (frontend) typically cut off requests if they don't get a response within 30 seconds. When the frontend hits a sleeping Render server, it waits 30 seconds, gets no reply, and throws a "Failed to send" error to the user, even though Render is still waking up in the background.

The Solutions Implemented:
To solve this without paying for a server upgrade, we implemented two strategies:
1. Extended Timeout: We adjusted the frontend's fetch or axios settings to have a timeout of 90 seconds (90,000 milliseconds). This ensures the browser waits long enough for Render to finish its 50-second wake-up cycle.
2. Caching: We added a memory cache to the backend. When Render wakes up and answers a question, it saves the answer in its RAM. If another student asks the exact same question within an hour, Render checks the cache first. If it finds the answer in the cache, it bypasses the Grok engine entirely and returns the answer instantly (under 1 second). 
The Long-Term Plan:
If the project secures funding or we successfully get the official xAI API key, we will solve the deployment permanently by doing two things:
1. Migrating to a paid Render tier (which eliminates the 50-second "sleep" delay).
2. Switching from the local ACP bridge to the cloud API. Cloud APIs reply in milliseconds and require almost zero RAM to run on the backend, making the whole system cheaper and faster. 

---

## 🧠 Important Engineering Lessons

This project was built with the goal of understanding the architecture rather than simply making the chatbot work.

Some of the major lessons from the project include:

- **An AI model and an AI API/provider are not the same thing.** The "brain" (model) and the "way we talk to it" (API/ACP) are separate.
- **AI providers should be abstracted behind interfaces.** We should be able to swap out Grok for OpenAI or another provider without rewriting the whole app.
- **External services can fail, so fallback systems are important.** If the AI fails, the app should still handle the error gracefully.
- **Verified application data should be separated from AI-generated responses.** Data from `programmes.json` is hard fact; AI answers are generated.
- **JSON can be useful as an early Knowledge Base before moving to a database.** It's fast to prototype with before investing in a real database.
- **Controllers should handle requests, while services handle business logic.** Separation of concerns makes the code cleaner and easier to test.
- **Repositories should handle data storage.** This keeps the logic separate from where the data actually lives.
- **Notification logic should remain separate from ticket logic.** Different functions should have their own dedicated areas.
- **Configuration and secrets should be handled through environment variables.** Never hardcode secrets in the source code.
- **A system should be designed so individual components can be replaced without rewriting the entire application.** This is the goal of modular architecture.

---

## 👨‍🏫 Mentorship & Engineering Approach

This project is being developed under the mentorship of a Senior Software Engineer.

The focus is not only on getting the chatbot to work, but understanding **why** the architecture is designed this way.

The main principle is:

> **Don't just copy the solution. Understand the architecture behind it.**

This project is also being documented in my personal engineering learning repository so that I can return to the problems I encountered, understand how they were solved, and reuse the lessons in future projects.