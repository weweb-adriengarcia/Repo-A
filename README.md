# Repo-A
This is a project that includes your files like .github/workflows 

# Setting Up Your CI to Use WeWeb export

Your GitHub Actions workflow needs to pull weweb code from your weweb repo and include it in your build. Here's how it works:


## How it works

1. You have Repo A (where your CI workflow runs along with files that you manage outside of weweb)
2. You have Repo B (WeWeb code)
3. Your CI in Repo A pulls the latest code from Repo B

## What you need to do

### Step 1: Create a Fine-Grained Personal Access Token

1. Go to GitHub Settings → Developer settings → Personal access tokens → Fine-grained tokens
2. Click "Generate new token"
3. Give it a name (e.g., "CI Access pull WeWeb")
4. Set expiration
5. Under "Repository access": Select "Only select repositories" and choose Repo B
6. Under "Permissions" → "Repository permissions": Set "Contents" to "Read-only"
7. Click "Generate token"
8. **Copy the token** you'll need it next

### Step 2: Add the token to your repo secrets

In **Repo A** (where your CI runs), create it if needed:
1. Go to Settings → Secrets and variables → Actions
2. Click "New repository secret"
3. Name: `GITHUB_TOKEN_PRIVATE_PULL_WEWEB`
4. Value: Paste the token you just created in **Step 1**
5. Click "Add secret"

### Step 3: Use it in your workflow

Create or update your `.github/workflows/build.yml`:

```yaml
name: Build with WeWeb export

on: [push, pull_request]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout Repo A
        uses: actions/checkout@v4

      - name: Checkout Repo B
        uses: actions/checkout@v4
        with:
          repository: weweb-adriengarcia/repo-b  # Your other private repo
          path: weweb-src                               # Where to place it
          token: ${{ secrets.GITHUB_TOKEN_PRIVATE_PULL_WEWEB }}

      - name: Build/Test
        run: |
          # Your build commands here
          # You can now use files from weweb-src/
```

## Important notes

- This fine-grained token only accesses Repo B (not all your repos)
- It's read-only, your CI can only pull code, never push
- The token automatically expires if you set that up, remember to refresh it and change `GITHUB_TOKEN_PRIVATE_PULL_WEWEB` when it happens
- You can revoke or rotate the token anytime in GitHub settings
- Each workflow run gets the latest code from Repo B automatically
- The token is only stored as a secret never commit it to your code
