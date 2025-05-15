
Enhanced PNG Compressor for Map Tiles

------------------------------------

This script optimizes PNG map tiles for the best balance between quality and file size,


**Installation requirements:**
	sudo apt install pngquant zopfli 
	cargo install oxipng # Requires Rust
# Basic usage - optimizes all PNGs in specified directory and subdirectories
```bash
uv run main.py /home/madson/Documents/Saafaa/output
```

# Adjust quality range (higher values = better quality)
```bash
uv run main.py /home/madson/Documents/Saafaa/output --min-quality 75 --max-quality 95
```

# Maximum compression (slower but best results)
```bash
uv run main.py /home/madson/Documents/Saafaa/output --level 6 --zopfli
```
