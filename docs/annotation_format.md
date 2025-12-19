# Annotation Format

Annotations are provided in a **single image-centric JSON file** mapping image filenames
to lists of bounding box annotations.

---

## JSON Structure

The annotation file follows this structure:

```json
{
  "image_name.png": [
    [x, y, width, height],
    [x, y, width, height],
    ...
  ],
  ...
}
