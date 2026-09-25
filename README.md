name: Generate Snake Animation

on:
  schedule:
    - cron: "0 0 * * *"      # regenerates daily at midnight UTC
  workflow_dispatch:          # lets you trigger it manually from the Actions tab
  push:
    branches:
      - main

jobs:
  generate:
    permissions:
      contents: write
    runs-on: ubuntu-latest
    steps:
      - name: Generate snake SVG from contribution graph
        uses: Platane/snk@v3
        with:
          github_user_name: ${{ github.repository_owner }}
          outputs: |
            dist/snake.svg
            dist/snake-dark.svg?palette=github-dark
            dist/snake-neon.svg?color_snake=%2300ff9c&color_dots=%23ff2e97,%2300e5ff,%23f7dc6f,%2300ff9c

      - name: Push generated SVGs to the "output" branch
        uses: crazy-max/ghaction-github-pages@v4
        with:
          target_branch: output
          build_dir: dist
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
