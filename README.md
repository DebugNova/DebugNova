name: 3D Contribution Calendar

# Renders your contribution calendar as an animated isometric 3D chart and
# commits the SVGs into profile-3d-contrib/. This replaces the dead
# github-readme-activity-graph and github-profile-trophy services.
#
# Offset 30 minutes from the snake job so the two pushes never race.

on:
  schedule:
    - cron: "47 2 * * *" # 02:47 UTC daily (08:17 IST)
  workflow_dispatch:

permissions:
  contents: write

jobs:
  build:
    runs-on: ubuntu-latest
    timeout-minutes: 10
    name: generate-github-profile-3d-contrib
    steps:
      - name: Checkout
        uses: actions/checkout@v4

      - name: Generate 3D contribution charts
        uses: yoshi389111/github-profile-3d-contrib@latest
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
          USERNAME: ${{ github.repository_owner }}
          MAX_REPOS: 100

      - name: Commit if changed
        run: |
          git config user.name  "github-actions[bot]"
          git config user.email "41898282+github-actions[bot]@users.noreply.github.com"
          git add profile-3d-contrib
          if git diff --quiet --cached; then
            echo "No change in 3D charts."
          else
            git commit -m "chore: update 3D contribution charts [skip ci]"
            git push
          fi
