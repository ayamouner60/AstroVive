# AstroVive

## Overview
AstroVive is a health and wellbeing web application designed for diagnostics, tracking user health data, managing reports, and monitoring potential health risks through an interactive dashboard and specialized views.

## Project Structure
- `app.py`: Main Flask application file for routing and server management.
- `diagnostic_engine.py`: Core logic engine for diagnostics and analysis.
- `diagnostic_streamlit.py`: Additional diagnostic interface built with Streamlit.
- `requirements.txt`: Python dependencies required for the project.
- `run.bat`: Batch script to automatically install requirements and run the application on Windows.
- `astra.db`: SQLite database for storing system and user data.
- `data/`: Contains application data files such as `astra_health_data.json`.
- `templates/`: HTML templates for the user interface pages (`dashboard.html`, `login.html`, `profile.html`, `diagnose.html`, `hazards.html`, `wellbeing.html`, `reports.html`, `details.html`, `voice.html`, `ground.html`, `body.html`, and `base.html`).
- `static/`: Static assets including CSS stylesheets (`style.css`) and JavaScript files (`app.js`, `diag.js`).
- `ui_config.json`: Configuration file for the user interface.

## Getting Started

### Prerequisites
Make sure you have Python installed on your system.

### Installation & Running
1. Clone the repository:
   ```bash
   git clone https://github.com/ayamouner60-x/AstroVive.git
   ```
2. Navigate to the project directory:
   ```bash
   cd AstroVive
   ```
3. Install the required dependencies:
   ```bash
   pip install -r requirements.txt
   ```
4. Run the application:
   - Using the batch script (Windows):
     ```bash
     run.bat
     ```
   - Or manually running Flask:
     ```bash
     python app.py
     ```