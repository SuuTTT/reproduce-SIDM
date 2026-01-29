# Colab Persistence Script for StarCraft II

When using Google Colab, you can avoid downloading the 5GB+ StarCraft II archive every time by persisting it on your Google Drive and using \`rsync\` to copy it to the local environment.

## 1. Mount Google Drive
\`\`\`python
from google.colab import drive
drive.mount('/content/drive')
\`\`\`

## 2. One-time Setup: Archive SC2 to Drive
Run this ONCE to save the installed SC2 and maps to your Drive.
\`\`\`bash
# Assuming SC2 is installed at /content/StarCraftII
tar -czf /content/drive/MyDrive/StarCraftII.tar.gz -C /content StarCraftII
\`\`\`

## 3. Fast Restore Script
Run this at the start of every session to quickly restore SC2.
\`\`\`python
import os

# Path to the archive on Drive
drive_archive = "/content/drive/MyDrive/StarCraftII.tar.gz"
local_target = "/content"

if os.path.exists(drive_archive):
    print("Restoring StarCraft II from Google Drive...")
    !tar -xzf {drive_archive} -C {local_target}
    print("Restoration complete.")
else:
    print("Archive not found on Drive. Please run installation script and save to Drive first.")
\`\`\`

## 4. Automation with rsync
If you prefer uncompressed files on Drive for even faster differential updates:
\`\`\`bash
# Sync from Drive to Local
rsync -avz --progress /content/drive/MyDrive/StarCraftII /content/
\`\`\`
