# Cluster Headache + Weather Tracker MVP

## Product goal
Help people with cluster headaches capture attacks quickly, correlate attacks with weather and known triggers, and share clear summaries with a neurologist.

## Core user flow
1. User logs a headache attack in under 30 seconds.
2. App automatically attaches local weather conditions at attack start time.
3. App analyzes attack history and shows likely correlations.
4. User exports a summary report (CSV or PDF) for clinical review.

## MVP features

### 1) Log an attack
Capture the following per attack record:
- Start time (required)
- End time (required)
- Pain level (e.g., 0-10 scale; required)
- Side: left or right (required)
- Oxygen used: yes/no (required)
- Potential triggers (multi-select):
  - Alcohol
  - Sleep loss
  - Heat
  - Smoke
  - Dehydration

Validation rules:
- End time must be after start time.
- Pain level must be within the configured scale.
- Trigger list may be empty.

### 2) Pull weather data automatically
For each attack, fetch and store weather at the user location and attack start timestamp:
- Temperature
- Humidity
- Barometric pressure
- Pressure trend (rising / steady / falling; include change rate)

MVP behavior:
- Weather lookup runs automatically after attack save.
- If weather API fails, keep attack and mark weather as pending/unavailable.
- Retry failed weather fetches in background.

### 3) Show patterns
Analytics views should include:
- Pattern callout examples:
  - "Attacks were more common when pressure dropped quickly."
- Charts by:
  - Hour of day
  - Day of week
  - Season/month
  - Pressure range buckets

Minimum analysis outputs:
- Attack frequency distribution by time windows.
- Attack rate by pressure-trend category.
- Trigger co-occurrence counts (e.g., dehydration + falling pressure).

### 4) Export for neurologist
Provide two export options:
- CSV (raw attack + weather rows)
- PDF summary (human-readable report)

PDF summary should include:
- Date range
- Total attacks
- Average pain level
- Most frequent side (left/right)
- Oxygen usage rate
- Top trigger frequencies
- Weather correlation highlights
- Included charts/snapshots

## Suggested data model (MVP)

### Attack
- `id`
- `start_time`
- `end_time`
- `pain_level`
- `side` (left/right)
- `oxygen_used` (boolean)
- `triggers` (array of enum values)
- `location_id`
- `created_at`

### WeatherSnapshot
- `id`
- `attack_id`
- `temperature_c` (or `_f`)
- `humidity_pct`
- `pressure_hpa`
- `pressure_trend` (rising/steady/falling)
- `pressure_delta_3h`
- `source`
- `fetched_at`
- `status` (ok/pending/failed)

## Non-functional MVP requirements
- Fast entry UX for in-attack use (mobile-first form).
- Basic privacy controls (user data scoped per account).
- Timezone-safe timestamps.
- Export output readable by clinicians and spreadsheet tools.

## Acceptance criteria
- User can save a complete attack in one form submission.
- At least 95% of saved attacks receive weather data within 2 minutes (when API available).
- User can view attack distributions across hour/day/season and pressure ranges.
- User can export both CSV and PDF for a selected date range.
