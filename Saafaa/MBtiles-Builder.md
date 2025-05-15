**Installation requirements:**
	uv add tqdm

This script efficiently converts a folder of XYZ tiles into MBtiles format
with parallel processing and progress tracking.


# Basic usage
```
python enhanced_mbtile_maker.py /path/to/xyz/tiles output_name
```

# With additional options
```
python enhanced_mbtile_maker.py /path/to/xyz/tiles output_name --name "My Map" --description "My custom map" --threads 8
```
