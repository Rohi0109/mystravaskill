---
name: strava
description: Get Strava activity data and athlete stats (requires API credentials).
homepage: https://developers.strava.com/docs/reference/
metadata: { "openclaw": { "emoji": "🏃", "requires": { "bins": ["curl"], "env": ["STRAVA_ACCESS_TOKEN", "STRAVA_REFRESH_TOKEN", "STRAVA_CLIENT_SECRET"] } } }
---

# Strava

Query Strava API for activities, stats, and athlete data.

## Authentication

Uses OAuth2 access tokens. Required environment variables:
- `STRAVA_ACCESS_TOKEN`: Current access token
- `STRAVA_REFRESH_TOKEN`: Refresh token for renewing access
- `STRAVA_CLIENT_SECRET`: Client secret from Strava API application

Access tokens expire after 6 hours. Use the refresh token to get a new one:

```bash
curl -X POST https://www.strava.com/oauth/token \
  -d client_id=YOUR_CLIENT_ID \
  -d client_secret=$STRAVA_CLIENT_SECRET \
  -d grant_type=refresh_token \
  -d refresh_token=$STRAVA_REFRESH_TOKEN
```

Response includes new `access_token` and `refresh_token`.

## Get Athlete Profile

```bash
curl -s -H "Authorization: Bearer $STRAVA_ACCESS_TOKEN" \
  "https://www.strava.com/api/v3/athlete"
```

Returns athlete ID, name, profile, stats summary.

## List Recent Activities

```bash
curl -s -H "Authorization: Bearer $STRAVA_ACCESS_TOKEN" \
  "https://www.strava.com/api/v3/athlete/activities?per_page=10"
```

Returns array of activities with: name, distance, moving_time, elapsed_time, type, start_date, average_speed, max_speed.

## Get Activity Details

```bash
curl -s -H "Authorization: Bearer $STRAVA_ACCESS_TOKEN" \
  "https://www.strava.com/api/v3/activities/{id}?include_all_efforts=true"
```

Replace `{id}` with activity ID. Returns full activity data including segments, splits, laps, GPS data.

## Get Athlete Stats

```bash
curl -s -H "Authorization: Bearer $STRAVA_ACCESS_TOKEN" \
  "https://www.strava.com/api/v3/athletes/{id}/stats"
```

Replace `{id}` with athlete ID (from profile endpoint). Returns totals and recent stats by activity type.

## Rate Limits

- 100 requests per 15 minutes
- 1,000 requests per day

Check `X-RateLimit-Limit` and `X-RateLimit-Usage` response headers.

## Error Handling

- 401: Access token expired or invalid (refresh token)
- 403: Rate limit exceeded
- 404: Resource not found

Always check response status before parsing JSON.

## Tips

- Use `per_page` and `page` params for pagination
- Filter by date: `?before=EPOCH&after=EPOCH`
- Activity types: Run, Ride, Swim, Hike, Walk, etc.
- Distances are in meters, times in seconds
- Speeds are in m/s (multiply by 3.6 for km/h)
