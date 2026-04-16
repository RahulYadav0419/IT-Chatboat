# IT Software Install Agent Hi HI Hi
A chat-based web tool that lets developers request software installations.
Uses Ollama (local AI) — no cloud, no cost.

---

## REQUIREMENTS
- Ubuntu server (where you will run this app)
- Python 3.10 or newer
- Ollama installed and running
- Internet connection (only for the agent server, not required on target machines)

---

## STEP 1 — Install Ollama
Run this on the machine where you will run this app:

    curl -fsSL https://ollama.ai/install.sh | sh
    ollama pull llama3.2

---

## STEP 2 — Install Python dependencies

    pip3 install -r requirements.txt

---

## STEP 3 — Start the app

    python3 app.py

Then open your browser and go to:

    http://localhost:5000

Or if running on a server, open:

    http://YOUR-SERVER-IP:5000

---

## HOW TO ADD NEW SOFTWARE
You do NOT need to touch any Python code.

Just open `config.json` and add a new entry inside "software": { }

Example — adding "htop":

    "htop": {
      "display_name": "htop",
      "description": "Interactive process viewer",
      "pre_install": ["sudo apt-get update -y"],
      "commands": ["sudo apt-get install -y htop"],
      "post_install": ["htop --version"]
    }

Save the file. The new software will appear immediately in the chat.

---

## SETTINGS (in app.py, top section)

| Setting       | Default                          | What it does                          |
|---------------|----------------------------------|---------------------------------------|
| OLLAMA_URL    | http://localhost:11434/api/generate | Where Ollama is running            |
| OLLAMA_MODEL  | llama3.2                         | Which Ollama model to use             |
| USE_OLLAMA    | True                             | Set False to skip AI (plain text)     |
| CONFIG_FILE   | config.json                      | Path to the software list             |

---

## FILE STRUCTURE

    it-install-agent/
    ├── app.py          ← Main application (Flask + logic)
    ├── config.json     ← Software list (YOU edit this to add software)
    ├── requirements.txt
    └── README.md

---

## SECURITY NOTES
- SSH passwords are stored only in the session cookie (not on disk)
- Sessions are cleared when the user closes the browser
- The app only allows software defined in config.json — users cannot request anything else
- For production use, run behind a reverse proxy (nginx) with HTTPS
- Change the secret key in app.py before deploying

---

## TROUBLESHOOTING

**Ollama responses are empty / app still works but no AI tone:**
→ Make sure Ollama is running: `ollama serve`
→ Make sure you pulled the model: `ollama pull llama3.2`
→ Or set `USE_OLLAMA = False` in app.py to skip AI entirely

**SSH connection fails:**
→ Make sure SSH is enabled on the target machine: `sudo systemctl enable ssh && sudo systemctl start ssh`
→ Check firewall: `sudo ufw allow ssh`

**Port 5000 already in use:**
→ Change the port at the bottom of app.py: `app.run(port=5001)`
