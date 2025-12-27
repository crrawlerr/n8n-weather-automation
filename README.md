This workflow is a complete daily weather automation built in n8n. It fetches weather data for one or more cities, generates a readable summary with simple alert logic, stores the results in Supabase, and sends a daily email with everything combined into one message. The workflow runs automatically every day using a Cron trigger.

# API setup and key configuration

The workflow uses the OpenWeatherMap API. After creating a free account on openweathermap.org, I generated an API key and added it to n8n. The workflow calls the standard current weather endpoint and requests metric units so the temperature and related values stay consistent.

The API returns fields such as temperature, feels_like, humidity, wind speed, pressure, visibility, and a short text description. These values are parsed and cleaned up before moving into the logic and storage steps.

Since n8n does not export credentials for security reasons, anyone importing this workflow will need to add their own OpenWeatherMap API key before running it.

# Supabase details

The workflow writes one row per city into a Supabase table named **weather_logs**.  
The table includes the following columns:

id  
run_at  
city  
temperature  
temperature_unit  
condition  
humidity  
wind_speed  
alert_type  
raw_response  
feels_like  
pressure  
visibility  
summary  

The workflow uses a Supabase credential configured inside n8n. The “Create Row” operation is used, and each field is mapped from the processed weather data. The run_at timestamp is generated inside n8n using `new Date().toISOString()`, so each entry is tied to the exact execution time.

As with the API key, Supabase credentials are not included in the exported workflow, so they must be added after import.

# Email configuration

The email is sent using the Gmail node in n8n. I connected my Gmail account using the built‑in OAuth credential type.

Since the workflow supports multiple cities and sends a single combined email, the subject line includes the current date, formatted using Luxon inside the expression editor. The Gmail node does not evaluate mixed text and expressions directly in the subject field, so the date is formatted entirely within the expression editor.

The body of the email contains the full combined summary for all cities, including temperature, feels like, humidity, wind speed, pressure, visibility, the weather description, and the alert type. The email is sent as plain text using a `<pre>` block, so the formatting stays aligned and easy to read.

Anyone importing the workflow will need to add their own Gmail credential before running it.

# How to import and run the workflow

To import the workflow:

1. Open n8n  
2. Go to the top‑right menu  
3. Choose **“Import from File”**  
4. Select **n8n-weather-workflow.json**

Before running it, make sure to set the following:

1. Add your OpenWeatherMap API key  
2. Add your Supabase credentials  
3. Add your Gmail credentials  
4. Update the city list in the “Set Cities” node if you want to change the locations  
5. Confirm the Cron trigger is set to the time you want the workflow to run each day  

You can run the workflow manually using the “Execute Workflow” button to verify everything works. Once the credentials and city list are set, the Cron node will take over and run it automatically every day.

# Included files

- **n8n-weather-workflow.json** - the exported workflow  
- **README.md** - this documentation  
- **workflow-overview.png** - a screenshot of the full workflow structure inside n8n
