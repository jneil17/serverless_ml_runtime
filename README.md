# Databricks Serverless ML Runtime

A collection of ready-to-use base environment YAML files that replicate Databricks Runtime ML functionality for serverless compute. These environments are compatible with Python 3.12 and serverless environment version 4.

## 🎯 What This Solves

Databricks Runtime ML provides a curated set of ML libraries, but it only works with classic compute clusters. **Serverless compute** is faster and more cost-effective, but doesn't have a "Runtime ML" option. 

This repository bridges that gap by providing YAML base environments that give you ML library collections similar to Databricks Runtime ML, optimized for serverless compute.

## ⚡ Quick Start

### 1. Clone the Repository

```bash
git clone https://github.com/jneil17/serverless_ml_runtime.git
cd serverless_ml_runtime
```

### 2. Upload to Databricks

**Option A: Via Git Integration**
1. In Databricks, go to **Workspace** > **Repos**
2. Click **Add Repo**
3. Enter: `https://github.com/jneil17/serverless_ml_runtime`
4. Click **Create Repo**

**Option B: Via Databricks UI**
1. In your Databricks workspace, go to **Workspace** > **Users** > your email
2. Right-click and select **Import**
3. Upload the YAML files from this repo (or drag and drop)
4. Recommended location: `/Workspace/Shared/environments/`

**Option C: Via Databricks CLI**
```bash
databricks workspace import-dir . /Workspace/Shared/environments/ml-runtimes
```

### 3. Use in a Notebook

**Method 1: Custom Base Environment (Per-Notebook)**
1. Create or open a notebook
2. Click **Connect** dropdown > Select **Serverless**
3. Click the **Environment** button (side panel)
4. Under **Base environment**, select **Custom**
5. Browse to your uploaded YAML file (e.g., `/Workspace/Shared/environments/databricks-ml-py312-notf.yml`)
6. Click **Apply**

**Method 2: Register as Workspace Base Environment (Admin)**
1. Go to **Settings** > **Workspace admin** > **Compute**
2. Under **Base environments for serverless compute**, click **Manage**
3. Click **Create new environment**
4. Name it (e.g., "ML Runtime - PyTorch")
5. Select your YAML file
6. Click **Create**

Now all users can select "ML Runtime - PyTorch" from the Base environment dropdown!

## 📦 Available Environments

### Recommended Files

| File | Packages | Startup | TensorFlow | PyTorch | Best For |
|------|----------|---------|------------|---------|----------|
| **databricks-ml-py312-notf.yml** | ~15 | 2 min | ❌ | ✅ | **Most users** (PyTorch + NLP) |
| **databricks-ml-py312-fixed.yml** | ~50 | 4 min | ❌ | ✅ | Comprehensive ML (CV, audio, time series) |
| **test-py312-minimal.yml** | 4 | 30 sec | ❌ | ❌ | Testing your setup |

### What's Included

**databricks-ml-py312-notf.yml** (Recommended):
- ✅ PyTorch 2.5.1 + torchvision
- ✅ Transformers, datasets, tokenizers (Hugging Face)
- ✅ XGBoost, LightGBM
- ✅ Ray (distributed training)
- ✅ Accelerate, sentence-transformers
- ✅ SHAP (model interpretation)
- ✅ Optuna (hyperparameter tuning)
- ✅ Plus all pre-installed packages (numpy, pandas, scikit-learn, matplotlib, seaborn, plotly)

**databricks-ml-py312-fixed.yml** (Comprehensive):
- Everything in the above, plus:
- ✅ Computer Vision: OpenCV, scikit-image, imageio
- ✅ Audio: librosa, soundfile, audioread
- ✅ Time Series: Prophet, pmdarima
- ✅ NLP: spaCy, NLTK, FastText
- ✅ Feature Engineering: category-encoders, imbalanced-learn
- ✅ Data Profiling: ydata-profiling
- ✅ Deep Learning: DeepSpeed, Keras 3, einops
- ✅ LLMs: LangChain, OpenAI client, tiktoken
- ✅ And more...

## ❗ Important: TensorFlow Note

**TensorFlow 2.18 is NOT included** because it's incompatible with numpy 2.1.3 (pre-installed in serverless environment 4).

**Your options:**
1. **Use PyTorch** (recommended) - Works perfectly, use the files above
2. **Wait for TensorFlow 2.19+** - Should support numpy 2.1 when released
3. **Use Keras 3 with JAX/PyTorch backend** - Get Keras API without TensorFlow

See [TENSORFLOW-NUMPY-CONFLICT.md](TENSORFLOW-NUMPY-CONFLICT.md) for details and workarounds.

## 🛠️ Creating Your Own Custom Environment

### Step 1: Understand the Format

Databricks serverless base environments use a simple YAML format:

```yaml
environment_version: '4'  # Always use quotes!

dependencies:
  - --index-url https://pypi.org/simple
  - package1==1.0.0
  - package2==2.0.0
  - package3>=3.0.0
```

**Key rules:**
- `environment_version` must be a string (use quotes)
- `dependencies` is a list (items start with `-`)
- First dependency should be the index URL
- Pin versions with `==` for reproducibility
- Use absolute paths for files: `/Workspace/...` or `/Volumes/...`

### Step 2: Create Your YAML File

**Method 1: Export from Notebook (Easiest)**

1. Open a notebook and connect to **Serverless** compute
2. Open **Environment** panel
3. Add packages you want
4. Click kebab menu (⋮) at bottom > **Export environment**
5. Save the YAML file
6. Upload to workspace

**Method 2: Write YAML Manually**

Create a new file (e.g., `my-custom-env.yml`):

```yaml
environment_version: '4'

dependencies:
  - --index-url https://pypi.org/simple
  
  # Your core ML packages
  - torch==2.5.1
  - transformers==4.47.1
  - xgboost==2.1.3
  
  # Domain-specific packages
  - opencv-python==4.10.0.84  # Computer vision
  - librosa==0.10.2           # Audio processing
  - spacy==3.8.3              # NLP
  
  # Your team's internal packages
  - /Workspace/Shared/packages/my-team-utils.whl
  
  # From git
  - git+https://github.com/your-org/internal-package.git
```

**Method 3: Start from Template**

Copy one of the provided YAML files and modify it:

```bash
cp databricks-ml-py312-notf.yml my-custom-env.yml
# Edit my-custom-env.yml to add/remove packages
```

### Step 3: Test Your Environment

1. Upload your YAML file to workspace
2. Create a test notebook
3. Use **Custom** base environment and select your file
4. Click **Apply**
5. Check the pip logs for any errors
6. Test that packages import correctly:

```python
# Test your packages
import torch
import transformers
import xgboost

print("✅ All packages loaded successfully!")
```

### Step 4: Share with Team (Optional)

**Register as a workspace base environment:**

1. Go to **Settings** > **Workspace admin** > **Compute**
2. Click **Manage** under **Base environments**
3. **Create new environment**
4. Name it and select your YAML file
5. Now everyone can use it from the dropdown!

### Step 5: Maintain Your Environment

**Updating packages:**

1. Edit your YAML file
2. Update package versions
3. Save the file
4. **Refresh** the environment:
   - Settings > Compute > Base environments
   - Find your environment > kebab menu (⋮) > **Refresh**
5. Users must restart notebooks to get updates

## 🔍 Tips for Custom Environments

### ✅ Best Practices

1. **Pin exact versions** for reproducibility:
   ```yaml
   - numpy==2.1.3      # ✅ Good
   - numpy>=2.1        # ⚠️ Okay but less reproducible
   - numpy             # ❌ Bad - version will change
   ```

2. **Don't re-install pre-installed packages** unless you need a specific version:
   ```yaml
   # These are already in serverless env 4, no need to specify:
   # - numpy==2.1.3
   # - pandas==2.2.3
   # - scikit-learn==1.6.1
   ```

3. **Use comments** to organize:
   ```yaml
   dependencies:
     # === CORE ML ===
     - torch==2.5.1
     
     # === NLP ===
     - transformers==4.47.1
     
     # === COMPUTER VISION ===
     - opencv-python==4.10.0.84
   ```

4. **Test incrementally** - Add packages in small groups and test

5. **Keep it focused** - Only include what you actually need (faster startup)

### ❌ Common Mistakes

1. **Wrong environment version format:**
   ```yaml
   environment_version: 4        # ❌ Wrong
   environment_version: '4'      # ✅ Correct
   ```

2. **Wrong dependencies format:**
   ```yaml
   dependencies:              # ❌ Wrong - this is conda format
     - pip:
       - package==1.0.0
   
   dependencies:              # ✅ Correct - Databricks format
     - --index-url https://pypi.org/simple
     - package==1.0.0
   ```

3. **Relative paths:**
   ```yaml
   - ./my-package.whl                    # ❌ Wrong
   - /Workspace/Shared/my-package.whl    # ✅ Correct
   ```

4. **Installing PySpark:**
   ```yaml
   - pyspark  # ❌ Never do this! Will break your session
   ```

5. **Conflicting versions:**
   ```yaml
   - tensorflow==2.18.0   # ❌ Conflicts with numpy 2.1.3
   - numpy==2.1.3
   ```

## 📚 What's Pre-installed in Serverless Environment 4?

You don't need to install these (they're already there):

**Core Data Science:**
- numpy 2.1.3
- pandas 2.2.3
- scipy 1.15.1
- scikit-learn 1.6.1

**Visualization:**
- matplotlib 3.10.0
- seaborn 0.13.2
- plotly 5.24.1

**ML Ops:**
- mlflow-skinny 2.22.0

**Utilities:**
- pyarrow 19.0.1
- boto3 1.36.2 (AWS)
- azure-storage-blob 12.23.0 (Azure)
- google-cloud-storage 3.1.0 (GCP)
- joblib 1.4.2
- tqdm, click, requests, pyyaml

[See full list in official docs](https://docs.databricks.com/aws/en/release-notes/serverless/environment-version/four)

## 🔧 Troubleshooting

### Environment won't apply

**Check:**
1. YAML syntax is correct (use [yamllint.com](https://www.yamllint.com/))
2. File path is absolute and accessible
3. `environment_version` has quotes: `'4'`
4. All package versions exist on PyPI

### Packages not installing

**Check pip logs:**
1. Environment panel > **pip logs** at bottom
2. Look for specific error messages
3. Common issues:
   - Package doesn't exist for Python 3.12
   - Version conflict with pre-installed packages
   - Network/download issues

### Startup is slow

**Solutions:**
1. Remove unnecessary packages
2. Make sure versions are pinned (enables caching)
3. Don't reinstall pre-installed packages
4. Use the minimal environment, add packages in notebook as needed

### Conflicts with pre-installed packages

**Solution:**
Don't fight the pre-installed versions. Build on top of them:

```yaml
dependencies:
  - --index-url https://pypi.org/simple
  # Don't specify numpy, pandas, scipy, scikit-learn
  # unless you absolutely need a different version
  - your-additional-packages-here
```

## 📖 Additional Resources

### Official Databricks Documentation
- [Databricks Runtime ML Overview](https://docs.databricks.com/aws/en/machine-learning/databricks-runtime-ml#photon-and-databricks-runtime-ml)
- [Serverless Environment Configuration](https://docs.databricks.com/aws/en/compute/serverless/dependencies#-select-an-environment-version)
- [Serverless Environment Version 4](https://docs.databricks.com/aws/en/release-notes/serverless/environment-version/four)
- [Serverless Compute Release Notes](https://docs.databricks.com/aws/en/release-notes/serverless/#environment-version)
- [Managing Base Environments](https://docs.databricks.com/aws/en/admin/workspace-settings/base-environment)

### Community Resources
- [Databricks Serverless Base Environments: The Straight Playbook (with YAML)](https://medium.com/endtoenddata/databricks-serverless-base-environments-the-straight-playbook-with-yaml-4915ab427dca)

### This Repository
- [TensorFlow NumPy Conflict Guide](TENSORFLOW-NUMPY-CONFLICT.md)
- [Python 3.12 Compatibility Notes](PYTHON312-COMPATIBILITY.md)
- [YAML Format Reference](YAML-FORMAT-GUIDE.md)

## 🤝 Contributing

Have improvements or additional environment configs? Contributions are welcome!

1. Fork this repository
2. Create your environment YAML
3. Test it thoroughly
4. Submit a pull request with:
   - Your YAML file
   - Description of what packages it includes
   - Use case (e.g., "NLP-focused", "Computer Vision", etc.)

## 📜 Version History

### Pre-installed Package Versions (Serverless Env 4)
- Python: 3.12.3
- NumPy: 2.1.3
- Pandas: 2.2.3
- Scikit-learn: 1.6.1

### This Repository
- Compatible with: Databricks Runtime (serverless) environment version 4
- Last updated: January 2026
- Maintained by: [@jneil17](https://github.com/jneil17)

## ⚠️ Known Limitations

1. **TensorFlow 2.18** is incompatible with numpy 2.1.3 (pre-installed)
   - Use PyTorch instead, or wait for TensorFlow 2.19+
   - See [TENSORFLOW-NUMPY-CONFLICT.md](TENSORFLOW-NUMPY-CONFLICT.md) for details

2. **Databricks-specific packages** are not available:
   - `databricks-automl-runtime` (use AutoML through UI)
   - `databricks-feature-engineering` (proprietary version)
   - Custom Databricks forks (use standard versions)

3. **Serverless compute limitations:**
   - No RDD APIs
   - No init scripts
   - No compute policies
   - See [full limitations](https://docs.databricks.com/aws/en/compute/serverless/limitations)

## 📄 License

This repository provides configuration files for use with Databricks. Individual packages have their own licenses.

## 🆘 Support

- **Issues**: [GitHub Issues](https://github.com/jneil17/serverless_ml_runtime/issues)
- **Databricks Support**: [Databricks Documentation](https://docs.databricks.com/)
- **Community**: [Databricks Community](https://community.databricks.com/)

---

**Made with ❤️ for the Databricks community**

If this helped you, give it a ⭐ on GitHub!
