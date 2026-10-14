### Flask Deployment and Application Servers — 14-10-2026

**Topics covered:**
- Flask application structure
- Development server vs production server
- WSGI fundamentals
- Running Flask with the Flask CLI and Python
- Running Flask using Gunicorn, uWSGI, and Waitress
- Hosting Flask with Apache `mod_wsgi`
- Nginx reverse proxy with Gunicorn
- Managing Flask with `systemd`
- Application logs, ports, and troubleshooting
- Production deployment best practices

**Practical commands:**
- `flask --app app run`
- `python app.py`
- `gunicorn --bind 127.0.0.1:8000 app:app`
- `uwsgi --http 127.0.0.1:8000 --module app:app`
- `waitress-serve --listen=127.0.0.1:8000 app:app`
- `curl http://127.0.0.1:8000/`
- `sudo ss -lntp`
- `sudo systemctl status flask-app`
- `sudo journalctl -u flask-app -f`

**Learning outcome:** Understood how to run a Flask application using development and production servers, how a WSGI server executes the application, and how reverse proxies and Linux service management support a production deployment.