# Rezing all images of a folder

#### Pedro Dousseau -  Jun 15th, 2017

### 1. Install imagemagick

### 2. Resize all images in a folder

```
$ convert '*.jpg[200x]' resized%03d.png

```

### 2. Shrink only the larger ones
```
convert '*.jpg[250x>]' thumb-300-%03d.png`

```
### 3. Keep file name
```
convert "images/*.jpg[250x>]" -set filename:base "%[base]" "images/new_folder/%[filename:base].jpg"
```
