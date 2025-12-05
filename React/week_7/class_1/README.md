# 📁 React File Uploads & Preview

React allows you to handle **file uploads** natively using the `<input type="file" />` element. You can also show a **preview** before uploading.

---

## 🧠 1. Basic Concept

1. **File input**: `<input type="file" />` lets users select files from their system.
2. **File object**: Browsers return selected files as **File objects**.
3. **State management**: Use `useState` to store selected files.
4. **Preview**: Use `URL.createObjectURL(file)` to display the file.

---

## 🔹 2. Handling File Input in React

```jsx
import React, { useState } from "react";

function FileUpload() {
  const [file, setFile] = useState(null);

  const handleChange = (e) => {
    setFile(e.target.files[0]); // Store the selected file in state
  };

  const handleSubmit = (e) => {
    e.preventDefault();
    console.log("Selected File:", file);
  };

  return (
    <form onSubmit={handleSubmit}>
      <input type="file" onChange={handleChange} />
      <button type="submit">Upload</button>
    </form>
  );
}

export default FileUpload;
```

✅ **Explanation**:

* `e.target.files` returns an array of selected files.
* `files[0]` is used because usually one file is selected.
* The file is stored in `useState` for further processing (upload or preview).

---

## 🔹 3. File Preview

### 📌 Preview Images

```jsx
import React, { useState } from "react";

function FilePreview() {
  const [file, setFile] = useState(null);
  const [preview, setPreview] = useState(null);

  const handleChange = (e) => {
    const selectedFile = e.target.files[0];
    setFile(selectedFile);

    // Generate preview URL
    if (selectedFile) {
      const previewUrl = URL.createObjectURL(selectedFile);
      setPreview(previewUrl);
    }
  };

  const handleSubmit = (e) => {
    e.preventDefault();
    console.log("File Submitted:", file);
  };

  return (
    <form onSubmit={handleSubmit}>
      <input type="file" onChange={handleChange} />
      {preview && (
        <div>
          <h4>Preview:</h4>
          <img
            src={preview}
            alt="Preview"
            style={{ width: "200px", height: "200px", objectFit: "cover" }}
          />
        </div>
      )}
      <button type="submit">Upload</button>
    </form>
  );
}

export default FilePreview;
```

✅ **Explanation**:

* `URL.createObjectURL(file)` creates a temporary URL to display the file in `<img>`.
* This method works for **images only**.
* `preview && ...` ensures preview shows only if a file is selected.

---

## 🔹 4. Handling Multiple File Uploads

```jsx
import React, { useState } from "react";

function MultipleFiles() {
  const [files, setFiles] = useState([]);
  const [previews, setPreviews] = useState([]);

  const handleChange = (e) => {
    const selectedFiles = Array.from(e.target.files); // Convert FileList to Array
    setFiles(selectedFiles);

    // Generate multiple previews
    const previewUrls = selectedFiles.map((file) => URL.createObjectURL(file));
    setPreviews(previewUrls);
  };

  const handleSubmit = (e) => {
    e.preventDefault();
    console.log("Files Submitted:", files);
  };

  return (
    <form onSubmit={handleSubmit}>
      <input type="file" multiple onChange={handleChange} />
      {previews.length > 0 && (
        <div style={{ display: "flex", gap: "10px", marginTop: "10px" }}>
          {previews.map((src, index) => (
            <img
              key={index}
              src={src}
              alt={`Preview ${index}`}
              style={{ width: "100px", height: "100px", objectFit: "cover" }}
            />
          ))}
        </div>
      )}
      <button type="submit">Upload All</button>
    </form>
  );
}

export default MultipleFiles;
```

✅ **Explanation**:

* `multiple` attribute allows users to select multiple files.
* `Array.from(e.target.files)` converts FileList to an array for easier manipulation.
* Previews are displayed using `map()`.

---

## 🔹 5. Removing Selected File Before Upload

```jsx
const removeFile = (index) => {
  const updatedFiles = [...files];
  updatedFiles.splice(index, 1);
  setFiles(updatedFiles);

  const updatedPreviews = [...previews];
  updatedPreviews.splice(index, 1);
  setPreviews(updatedPreviews);
};
```

* Add a **remove button** below each preview image to allow users to delete a selected file.

---

## 🔹 6. Uploading Files to Server

Without any library, you can use **`FormData`**:

```jsx
const handleUpload = async () => {
  const formData = new FormData();
  formData.append("file", file); // for single file
  // for multiple files: files.forEach(f => formData.append("files", f))

  const response = await fetch("/upload", {
    method: "POST",
    body: formData,
  });

  const data = await response.json();
  console.log("Upload Response:", data);
};
```

✅ **Explanation**:

* `FormData` automatically sets `Content-Type` to `multipart/form-data`.
* Works with **file inputs** and other form fields simultaneously.

---

## 🔹 7. Handling File Type & Size Validation

```jsx
const handleChange = (e) => {
  const selectedFile = e.target.files[0];

  if (!selectedFile.type.startsWith("image/")) {
    alert("Only image files are allowed!");
    return;
  }

  if (selectedFile.size > 2 * 1024 * 1024) {
    alert("File size should be less than 2MB");
    return;
  }

  setFile(selectedFile);
  setPreview(URL.createObjectURL(selectedFile));
};
```

* Validates **file type** and **size** before upload.
* Enhances UX and prevents invalid files from being uploaded.

---

## 🔹 8. Cleaning Up Object URLs

To prevent **memory leaks**, always revoke URLs after component unmount:

```jsx
useEffect(() => {
  return () => {
    if (preview) URL.revokeObjectURL(preview);
  };
}, [preview]);
```

---

## 🔹 9. Complete Example — Single Image Upload with Preview

```jsx
import React, { useState, useEffect } from "react";

function FileUploadPreview() {
  const [file, setFile] = useState(null);
  const [preview, setPreview] = useState(null);

  const handleChange = (e) => {
    const selectedFile = e.target.files[0];
    if (!selectedFile) return;

    if (!selectedFile.type.startsWith("image/")) {
      alert("Please select an image file");
      return;
    }

    setFile(selectedFile);
    setPreview(URL.createObjectURL(selectedFile));
  };

  const handleSubmit = (e) => {
    e.preventDefault();
    if (!file) return;
    console.log("Uploading:", file);
  };

  useEffect(() => {
    return () => {
      if (preview) URL.revokeObjectURL(preview);
    };
  }, [preview]);

  return (
    <form onSubmit={handleSubmit}>
      <input type="file" onChange={handleChange} />
      {preview && <img src={preview} alt="Preview" width={200} />}
      <button type="submit">Upload</button>
    </form>
  );
}

export default FileUploadPreview;
```

---

## ✅ Summary / Best Practices

| Topic          | Best Practices                                        |
| -------------- | ----------------------------------------------------- |
| File Input     | Use `type="file"`, store in state                     |
| Preview        | Use `URL.createObjectURL()`                           |
| Multiple Files | Use `multiple` attribute and `Array.from()`           |
| Remove Files   | Maintain preview & files array, provide remove option |
| Validation     | Check type & size before uploading                    |
| Uploading      | Use `FormData`, POST via fetch                        |
| Cleanup        | Revoke object URLs on unmount (`URL.revokeObjectURL`) |

---

# 🏠 Project: **“My Photo Album”**

### **Project Description:**

A simple React app where users (kids) can **upload images**, see a **preview**, and maintain a **small photo album gallery** on the page.

No backend required – files just stay in memory/state.

---

## **Features**

1. **Upload Single or Multiple Images**

   * Select one or many images from your computer.
   * Show a preview before adding them to the album.

2. **Preview Before Adding**

   * Display the image immediately after selection using `URL.createObjectURL`.

3. **Add to Album**

   * Once happy with preview, click **“Add to Album”**.
   * Image gets added to a gallery below.

4. **Remove Images**

   * Remove unwanted images from the album.

5. **Drag & Drop (Optional Advanced)**

   * Users can drag files onto a box to upload images.

6. **Simple Styling**

   * Images arranged nicely in a grid.
   * Hover effects or borders to make it fun.

---

## **Folder Structure (React)**

```
my-photo-album/
│
├─ src/
│  ├─ components/
│  │  ├─ FileUploader.jsx
│  │  └─ PhotoGallery.jsx
│  ├─ App.jsx
│  └─ index.js
├─ package.json
└─ README.md
```

---

## **Example Code**

### **FileUploader.jsx**

```jsx
import React, { useState, useEffect } from "react";

export default function FileUploader({ addToAlbum }) {
  const [selectedFiles, setSelectedFiles] = useState([]);
  const [previews, setPreviews] = useState([]);

  const handleChange = (e) => {
    const files = Array.from(e.target.files);
    setSelectedFiles(files);

    const previewUrls = files.map((file) => URL.createObjectURL(file));
    setPreviews(previewUrls);
  };

  const handleAddToAlbum = () => {
    addToAlbum(selectedFiles, previews);
    setSelectedFiles([]);
    setPreviews([]);
  };

  useEffect(() => {
    return () => previews.forEach((url) => URL.revokeObjectURL(url));
  }, [previews]);

  return (
    <div>
      <input type="file" multiple onChange={handleChange} />
      {previews.length > 0 && (
        <div style={{ display: "flex", gap: "10px", marginTop: "10px" }}>
          {previews.map((url, index) => (
            <img
              key={index}
              src={url}
              alt={`Preview ${index}`}
              style={{ width: "100px", height: "100px", objectFit: "cover" }}
            />
          ))}
        </div>
      )}
      {previews.length > 0 && (
        <button onClick={handleAddToAlbum}>Add to Album</button>
      )}
    </div>
  );
}
```

---

### **PhotoGallery.jsx**

```jsx
import React from "react";

export default function PhotoGallery({ album, removePhoto }) {
  return (
    <div style={{ display: "flex", flexWrap: "wrap", gap: "10px", marginTop: "20px" }}>
      {album.map((photo, index) => (
        <div key={index} style={{ position: "relative" }}>
          <img
            src={photo.preview}
            alt={`Album ${index}`}
            style={{ width: "150px", height: "150px", objectFit: "cover", borderRadius: "8px" }}
          />
          <button
            onClick={() => removePhoto(index)}
            style={{
              position: "absolute",
              top: "5px",
              right: "5px",
              background: "red",
              color: "white",
              border: "none",
              borderRadius: "50%",
              width: "25px",
              height: "25px",
              cursor: "pointer"
            }}
          >
            ×
          </button>
        </div>
      ))}
    </div>
  );
}
```

---

### **App.jsx**

```jsx
import React, { useState } from "react";
import FileUploader from "./components/FileUploader";
import PhotoGallery from "./components/PhotoGallery";

function App() {
  const [album, setAlbum] = useState([]);

  const addToAlbum = (files, previews) => {
    const newPhotos = files.map((file, index) => ({ file, preview: previews[index] }));
    setAlbum((prev) => [...prev, ...newPhotos]);
  };

  const removePhoto = (index) => {
    setAlbum((prev) => prev.filter((_, i) => i !== index));
  };

  return (
    <div style={{ padding: "20px" }}>
      <h1>📸 My Photo Album</h1>
      <FileUploader addToAlbum={addToAlbum} />
      <PhotoGallery album={album} removePhoto={removePhoto} />
    </div>
  );
}

export default App;
```

---