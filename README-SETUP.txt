# AMS Home Screen

This repository contains only the public AMS launch page and app icons. It does not contain business records, Apps Script source code, or credentials.

## Publish with GitHub Pages

1. Upload every file in this folder to the root of `abba-ijah/ams-home` (the same level as README.md).
2. In the repository, open **Settings → Pages**.
3. Under **Build and deployment**, choose **Deploy from a branch**.
4. Select branch **main** and folder **/(root)**, then Save.
5. Wait for GitHub Pages to finish publishing.

Expected site: https://abba-ijah.github.io/ams-home/

## Add to iPhone Home Screen

1. Open the published site in Safari.
2. Wait for it to redirect to AMS once. Then return to the published site address in Safari if needed.
3. Tap Share → Add to Home Screen.
4. Confirm the name is **AMS**, then tap Add.
5. Delete any previous AMS shortcut first if it has the old icon.

The page declares `apple-touch-icon.png` for Safari and uses the title/short name AMS. iOS may cache old icons; remove the old shortcut before adding the new one.

## App destination

The launch page redirects to the existing Google Apps Script web app URL supplied by the owner.
