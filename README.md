# Athlete Workload & Injury-Risk Intelligence Platform

A single dashboard that merges training, match, wearable, recovery and medical data, flags abnormal
workload and fatigue patterns, and gives practical recommendations to coaches and medical staff.

## Run it

```bash
pip install -r requirements.txt
python generate_data.py      # creates the 5 CSV files in data/
streamlit run app.py         # opens the dashboard in your browser
```

## Project structure

| File | Purpose |
|------|---------|
| `generate_data.py` | Creates synthetic data for 15 athletes x 90 days (5 separate CSV sources) |
| `metrics.py` | Merges sources; computes acute/chronic load, ACWR, monotony, strain, readiness |
| `risk_rules.py` | Rule-based flags (red/amber/green) + Isolation Forest anomaly detection |
| `recommendations.py` | Converts flags into coach and medical actions |
| `auth.py` | Login and signup (SQLite, hashed passwords, lockout after 5 wrong attempts) |
| `app.py` | Streamlit dashboard (5 pages, behind the login) |

## Login

- First screen is **Log in / Sign up**. Staff choose a role (Coach or Medical staff); the Alerts page opens on that role's actions.
- Demo account for presentations: username `coach`, password `Coach@123` (or click "Try the demo account").
- Passwords are stored as PBKDF2-SHA256 hashes with a random salt, never as plain text. Users are saved in `users.db` (created automatically).
- For a real deployment: remove the demo account, serve over HTTPS and add password reset / email verification.

## How it works

- **ACWR** = 7-day average load / 28-day average load. 0.8-1.3 is the sweet spot; above 1.5 is high injury risk.
- **Readiness (0-100)** = 100 minus penalties for HRV drop (25), sleep (20), soreness (20), fatigue (15), resting HR (10), ACWR (10).
- **Rules** give a reason for every flag. **Isolation Forest** is a second opinion for unusual combinations.
- Each athlete is compared with **their own baseline**.

## Demo scenarios planted in the data

- **A07 Siddharth Nair:** load spike + poor sleep -> high risk
- **A03 Vikram Singh:** normal load, but hidden fatigue (HRV down, soreness up)
- **A11 Yash Deshmukh:** back from injury, load ramped too fast -> ACWR 2.4

## Limitations

Synthetic data; thresholds come from sports-science practice and need tuning per sport.
Decision support only, not a medical diagnosis.

## Future scope

Live wearable APIs (Garmin, Whoop), a mobile app, a model trained on real injury history, multi-season trends.
