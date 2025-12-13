# Models

Store trained RL agent checkpoints here.
- **Commit Strategy**: Large binary files (`*.pth`, `*.pt`, `*.onnx`) should be **IGNORED** by git to avoid hitting limits.
- Use **Git LFS** (Large File Storage) if you must version control models, or store them in cloud storage (S3/GCS) and pull them during deployment.
