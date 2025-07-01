# Development Setup Guide: pp-qr-code Internal Package

This guide covers how to test improvements to the `pp-qr-code` package with your LiquidQR application and set it up as a permanent internal tool.

## 🏗️ Package Build Status
✅ Package built successfully - `lib` directory contains compiled output

## 🚀 Option 1: npm link (Immediate Testing)

**Best for**: Rapid development and testing of changes

### Setup Steps:

1. **Create the link from pp-qr-code package:**
```bash
# In pp-qr-code-styling directory (current directory)
npm link
```

2. **Link to your LiquidQR project:**
```bash
# Navigate to LiquidQR client directory
cd ../LiquidQR/client
npm link pp-qr-code
```

3. **Test and rebuild workflow:**
```bash
# When you make changes to pp-qr-code:
cd ../../pp-qr-code-styling
npm run build:dev  # or npm run build for production

# Your LiquidQR client will automatically use the updated version
cd ../LiquidQR/client
npm start  # Your changes are now active
```

### Pros:
- ✅ Immediate testing
- ✅ Live updates during development
- ✅ No need to modify package.json

### Cons:
- ⚠️ Can be fragile with npm installs
- ⚠️ Other team members need to set up links too

---

## 🔧 Option 2: File Path Dependency (Simple & Reliable)

**Best for**: Consistent team development

### Setup:

1. **Modify LiquidQR/client/package.json:**
```json
{
  "dependencies": {
    "pp-qr-code": "file:../../pp-qr-code-styling",
    // ... other dependencies
  }
}
```

2. **Install the local package:**
```bash
cd LiquidQR/client
npm install
```

3. **Development workflow:**
```bash
# When you make changes:
cd ../../pp-qr-code-styling
npm run build

cd ../LiquidQR/client
npm install  # Reinstalls your local package
npm start
```

### Pros:
- ✅ More reliable than npm link
- ✅ Works for entire team
- ✅ Clear dependency in package.json

### Cons:
- ⚠️ Need to reinstall after each build
- ⚠️ Package.json shows local path (not ideal for production)

---

## 🏢 Option 3: Internal Package Repository (Production Ready)

**Best for**: Long-term internal tool with proper versioning

### 3a. Private npm Registry (Recommended)

Use [Verdaccio](https://verdaccio.org/) for a lightweight private registry:

1. **Install Verdaccio globally:**
```bash
npm install -g verdaccio
```

2. **Start the registry:**
```bash
verdaccio
# Runs on http://localhost:4873
```

3. **Configure npm to use local registry:**
```bash
# Set registry for pp-qr-code scope
npm config set @your-org:registry http://localhost:4873
```

4. **Update package name to use scope:**
```json
// pp-qr-code-styling/package.json
{
  "name": "@your-org/pp-qr-code",
  "version": "0.6.5-internal.1"
}
```

5. **Publish to internal registry:**
```bash
# In pp-qr-code-styling
npm run build
npm publish --registry http://localhost:4873
```

6. **Use in LiquidQR:**
```json
// LiquidQR/client/package.json
{
  "dependencies": {
    "@your-org/pp-qr-code": "^0.6.5-internal.1"
  }
}
```

### 3b. Git-based Package (Simpler Alternative)

1. **Create a separate repo for the package** (or use existing):
```bash
# Push your pp-qr-code-styling to a private repo
git remote add origin git@github.com:your-org/pp-qr-code-internal.git
git push -u origin main
```

2. **Tag releases:**
```bash
git tag v0.6.5-internal.1
git push origin v0.6.5-internal.1
```

3. **Install in LiquidQR:**
```json
// LiquidQR/client/package.json
{
  "dependencies": {
    "pp-qr-code": "git+ssh://git@github.com:your-org/pp-qr-code-internal.git#v0.6.5-internal.1"
  }
}
```

---

## 🛠️ Development Workflow Scripts

Create these helper scripts for easier development:

### pp-qr-code-styling/scripts/dev-link.sh
```bash
#!/bin/bash
echo "Building package..."
npm run build:dev

echo "Refreshing link to LiquidQR..."
cd ../LiquidQR/client
npm link pp-qr-code

echo "✅ Development build linked to LiquidQR"
echo "Run 'npm start' in LiquidQR/client to test"
```

### pp-qr-code-styling/scripts/build-and-test.sh
```bash
#!/bin/bash
echo "Building production package..."
npm run build

echo "Running tests..."
npm test

echo "Linking to LiquidQR for testing..."
cd ../LiquidQR/client
npm link pp-qr-code

echo "✅ Ready for testing in LiquidQR"
```

Make scripts executable:
```bash
chmod +x scripts/*.sh
```

---

## 🔄 Recommended Development Workflow

### For Active Development (Option 1 - npm link):

1. **Initial setup (once):**
```bash
# Setup the link
cd pp-qr-code-styling
npm link
cd ../LiquidQR/client  
npm link pp-qr-code
```

2. **Daily development:**
```bash
# Make changes to pp-qr-code
cd pp-qr-code-styling
# Edit source files...
npm run build:dev  # Fast development build
# Test in LiquidQR immediately
```

3. **Before committing:**
```bash
npm run build     # Production build
npm test         # Run tests
```

### For Team/Production (Option 3 - Internal Registry):

1. **Setup internal registry** (one-time team setup)
2. **Version your changes:**
```bash
npm version patch  # or minor/major
npm run build
npm publish --registry http://localhost:4873
```
3. **Update LiquidQR:**
```bash
cd ../LiquidQR/client
npm update @your-org/pp-qr-code
```

---

## 🧪 Testing Strategy

### 1. Automated Testing in Package:
```bash
cd pp-qr-code-styling
npm test  # Run existing tests
```

### 2. Integration Testing with LiquidQR:
```bash
cd LiquidQR/client
npm start
# Test QR rendering in grid/list views
# Test custom download functionality
# Test intersection observer performance
```

### 3. Performance Testing:
Create test pages with:
- 100+ QRs in grid view
- Rapid scrolling
- Memory usage monitoring
- Download functionality

---

## 📋 Next Steps

1. **Choose your approach:**
   - Use **Option 1** for immediate testing
   - Use **Option 2** for stable team development  
   - Use **Option 3** for production-ready internal package

2. **Set up the chosen workflow**

3. **Test that the current build works** before making improvements

4. **Start with critical improvements** from the task list

Would you like me to help you set up any of these approaches right now? 