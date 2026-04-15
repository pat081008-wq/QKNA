# Obsidian Geocore Analytics — KNA Dashboard

Kriging Neighbourhood Analysis (KNA) tool for qualitative estimation parameter optimisation. Evaluates **Kriging Efficiency (KE)** and **Slope of Regression** across combinations of block size, search distance, sample count, and discretisation points.

Built for [Obsidian Consulting Services](https://obsidianconsulting.co.za) / MREC Iberico.

---

## Repository Structure

```
├── index.html          # Login page (entry point)
├── dashboard.html      # KNA application (loaded after login)
├── credentials.json    # User credentials — edit this to manage access
└── README.md
```

---

## Deployment (GitHub Pages)

1. Push this repository to GitHub
2. Go to **Settings → Pages**
3. Under **Source**, select `main` branch → `/ (root)` → **Save**
4. Your site will be live at `https://<your-username>.github.io/<repo-name>/`

---

## Managing Users

Edit `credentials.json` to add, remove, or change users. Usernames and passwords support **any characters including spaces**.

```json
{
  "users": [
    {
      "username": "admin",
      "password": "obsidian2025",
      "display": "Administrator"
    },
    {
      "username": "client demo",
      "password": "demo access 1",
      "display": "Demo Client"
    }
  ]
}
```

| Field | Description |
|---|---|
| `username` | Login username — any characters, case-sensitive |
| `password` | Login password — any characters, case-sensitive |
| `display` | Name shown in the session bar after login |

After editing `credentials.json`, commit and push the change. GitHub Pages typically updates within 1–2 minutes.

---

## ⚠️ Security Notice

This is a **static site** — all files including `credentials.json` are publicly accessible via direct URL. This provides **UI-level access control only** (keeps casual visitors out), not cryptographic security.

**Do not use this to protect:**
- Confidential client data or proprietary datasets
- Commercially sensitive information that must remain private
- Anything subject to data protection regulations

For stronger security, consider deploying behind a server-side authentication layer (e.g. Netlify Identity, Cloudflare Access, or a Node/Python backend).

---

## KNA Workflow

### 1. Data Tab
- Upload **Collar**, **Survey**, and **Assay** CSV files
- Map columns (Hole ID, XYZ, From/To, Grade)
- Select **desurveying method**: Minimum Curvature, Tangential, or Average Angle
- Optionally filter by a **domain/zone column**
- Click **Desurvey & Composite Data**

### 2. Variogram Tab
- Choose file format: **GSLIB** (`.var`), **Supervisor/Datamine** (`.par`/`.out`), **Isatis** (`.vgm`), or **Manual Entry**
- Define nugget, nested structures (spherical / exponential / Gaussian), sill and ranges
- Set search ellipse orientation (azimuth, dip, plunge) and anisotropy ranges
- Click **Apply Variogram Model**

### 3. Params Tab
- Set **min/max samples**, **search distance sweep** (from / to / step), and **discretisation points** (X, Y, Z)
- Optionally override the **block centroid** (defaults to data mean)

### 4. Blocks Tab
- Add block size combinations (X × Y × Z in metres)
- Choose **In-Browser Engine** or **Upload Pre-computed Results**

### 5. Results
- **KNA Chart**: KE (%) on left axis, Slope on right axis, vs any parameter on X-axis
- **KE vs Slope Scatter**: Pareto view — top-right quadrant is optimal
- **Results Table**: All combinations ranked by KE with colour-coded assessment
- **Export CSV**: Download full results for reporting

---

## Interpretation Guide

| Metric | Target | Meaning |
|---|---|---|
| Kriging Efficiency (KE) | ≥ 50% | Acceptable local accuracy; low over-smoothing |
| Slope of Regression | ≥ 0.90 | Good relationship between estimated and true grades |
| Both below threshold | — | Over-smoothed; increase search or reduce block size |

---

## Technical Notes

- Pure client-side — no server, no build toolchain required
- CDN dependencies: [Chart.js 4.4](https://www.chartjs.org/), [PapaParse 5.4](https://www.papaparse.com/)
- Kriging engine: Ordinary Kriging with Gaussian elimination (partial pivot), block variance via discretisation point integration
- Large arrays handled via explicit loops (no spread operators) to avoid stack overflow on large datasets
- Supports scientific notation coordinates in CSV without precision loss
