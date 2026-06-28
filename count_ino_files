name: Count .ino Files

on:
  push:
    branches:
      - main
      - master
  workflow_dispatch:

jobs:
  count-ino-files:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout repository
        uses: actions/checkout@v4

      - name: Set up Python
        uses: actions/setup-python@v5
        with:
          python-version: '3.x'

      - name: Count .ino files
        run: |
          echo "Counting .ino files in the repository..."
          python3 - << 'EOF'
          import os
          ino_count = sum(len([f for f in files if f.endswith('.ino')]) for _, _, files in os.walk('.'))
          print(f"Total .ino files found: {ino_count}")
          EOF
