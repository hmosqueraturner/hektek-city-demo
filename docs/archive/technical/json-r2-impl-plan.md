# Implementation Plan - JSON Configuration via Cloudflare R2

## Goal
Migrate static JSON configuration files (`visual-states.json`, `tours-config.json`, `content-sources.json`) to Cloudflare R2 to enable real-time updates without requiring a site redeployment.

## Security & Architecture Analysis

### Is this secure?
**Yes, if configured correctly.**

1.  **Read-Only Access**:
    *   The URL `https://assets.hectortechno.com/config/tours.json` will be **publicly readable**. This is necessary for the browser to fetch it.
    *   However, R2 buckets are **private by default for writing**. A user cannot upload, modify, or delete files just by knowing the URL. They would need your secret AWS S3 Access Keys, which are never exposed to the client.

2.  **Data Sensitivity**:
    *   The files (`tours-config.json`, `visual-states.json`) contain **presentation logic**, not secrets.
    *   **Rule**: Never put API Keys, passwords, or user data in these JSONs.
    *   If a malicious user downloads `tours.json`, they only see how your tour works (which they can see anyway by using the site).

3.  **Integrity**:
    *   **Risk**: A user could technically intercept the network request on *their own machine* and swap the JSON.
    *   **Impact**: They only break the site for themselves. They cannot affect other users.
    *   **Mitigation**: The application should have robust error handling (try/catch) when parsing the JSON to prevent crashes if the file is malformed.

4.  **CORS (Cross-Origin Resource Sharing)**:
    *   Configure R2 to only allow `GET` requests from `https://hectortechno.com` (and localhost for dev). This prevents other malicious sites from embedding your configs (though for public data, this is low risk).

## Proposed Changes

### 1. R2 Structure
We will create a `/config` directory in your R2 bucket and upload the files there.

```text
/config
  ├── visual-states.json
  ├── tours-config.json
  └── content-sources.json
```

### 2. New Hook: `useRemoteConfig`
We will create a hook that attempts to fetch the config from R2, and falls back to the local version if the fetch fails or is disabled.

#### [NEW] [src/hooks/useRemoteConfig.js](../../../../src/hooks/useRemoteConfig.js)
-   **Input**: `configName` (e.g., 'tours'), `localFallback`.
-   **Logic**:
    1.  Check `content-sources.json` to see if `configs` is enabled.
    2.  If enabled, `fetch(${baseUrl}/${configName}.json)`.
    3.  If fetch fails or disabled, return `localFallback`.
    4.  Use `stale-while-revalidate` pattern (optional) or simple `useEffect`.

### 3. Component Updates
We need to refactor components to use this async hook instead of direct imports.

#### [MODIFY] [src/hooks/useTour.js](../../../../src/hooks/useTour.js)
-   Remove direct import of `tours-config.json`.
-   Use `useRemoteConfig('tours-config', localToursConfig)`.

#### [MODIFY] [src/components/MapRPG.jsx](../../../../src/components/MapRPG.jsx)
-   Use `useRemoteConfig` for `visual-states`.

### 4. Update Content Sources
#### [MODIFY] [src/config/content-sources.json](../../../../src/config/content-sources.json)
-   Set `configs.enabled` to `true`.

## Verification Plan
1.  **Upload**: Manually upload JSONs to R2 (User task or simulated).
2.  **Dev Test**: Run `npm run dev`. Verify network tab shows requests to `assets.hectortechno.com/config/...`.
3.  **Fallback Test**: Temporarily break the URL. Verify site still loads using local JSON.
4.  **Runtime Update**: Modify JSON in R2. Refresh page. Verify changes appear immediately.
