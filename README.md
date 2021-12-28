# CKEditor5-React-SparkMinds
This is a version customize using from packages CKEditor5. You can download and import packages from homepages of CKEdtor to create collaboration yourself or using tool online CKEitor 5 Online Builder.
Or install packages builded 👇

## Installation

```shell-script
npm install @ckeditor/ckeditor5-react

npm install @sparkminds/custom-ckeditor-build-dev
```

## Usage

### Integrate React

```js
import { CKEditor } from "@ckeditor/ckeditor5-react";
import CustomBuildEditor from "@sparkminds/custom-ckeditor-build-dev/ckeditor.js";

<CKEditor
  required
  editor={CustomBuildEditor}
  onReady={(editor) => {
    // You can store the "editor" and use when it is needed.
    console.log("Editor is ready to use!", editor);
  }}
  onChange={(event, editor) => {
    const data = editor.getData();
    console.log({ event, editor, data });
  }}
  onBlur={(event, editor) => {
    console.log("Blur.", editor);
  }}
  onFocus={(event, editor) => {
    console.log("Focus.", editor);
  }}
  onInit={(event, editor) => {
    // Called here in case using DecoupledEditor not build or from src
    // In here will set CSS for editor
    // Example:
    // editor.ui.getEditableElement().parentElement.insertBefore(
    // editor.ui.view.toolbar.element,
    // editor.ui.getEditableElement()
  }}
/>;
```
### How to show toolbar in CKEditor

```js
<CKEditor
  required
  editor={CustomBuildEditor}
  // do something
  config={
      toolbar: {
          items: [
            'bold',
            'italic',
            'strikethrough',
            'underline', 
            'subscript', 
            'superscript', '|',
             '-',
            'heading', '|',
            'undo',
            'redo', '|',
          ]
      }
  }
/>;
```

- Besides button names, you can also use the dedicated separators for toolbar groups ('|') and toolbar lines ('-').

### Config plugins:

- Example:

```js
//...
config={
    toolbar: {
        items: [
            //...
        ]
        image: {
            resizeUnit: 'px',
            toolbar: [
                'imageStyle:inline',
                'imageStyle:block',
                'imageStyle:side',
                '|',
                'toggleImageCaption',
                'imageTextAlternative',
            ],
            styles: ['full', 'alignLeft', 'alignRight'],
            type: ['JPEG', 'JPG', 'GIF', 'PNG'],
        },
    },
}
```

### Custom Upload Adapter

- Set ***extraPlugins*** to config

```js
<CKEditor>
    required
    editor={CustomBuildEditor}
    config={
        extraPlugins: [CustomUploadAdapterPlugin]
        toolbar : {
            items: //...
        }
        image: {
            //... 
        }
        //..
    }
/>

// loader: file upload. Default will Base64 in case not internet or server error(in package imported and config Base64)

function CustomUploadAdapterPlugin(editor) {
  editor.plugins.get('FileRepository').createUploadAdapter = (loader) => {
    return new UploadAdapter(loader); // Here return Promise 😂
  };
}
```

- ***Interface UploadAdapter***: method upload() will return a promise: 
    - resolved by a successful upload with an object containing information about the uploaded file.
    - rejected because of an error, in which case nothing is inserted into the content.

```js
class UploadAdapter {
  constructor(loader) {
    this.loader = loader;
  }
  upload() {
    return this.loader.file.then(
      uploadedFile => new Promise((res, rej) => {
          // CALL API
          upLoadImageBefore().then((r) => {
            // Data will response in here
            // Handle result response in here and return resolve or rejected
          });
        }),
    );
  }
}
```

- Example UploadAdapter

```js
class UploadAdapter {
  constructor(loader) {
    this.loader = loader;
  }
  upload() {
    return this.loader.file.then(
      uploadedFile => new Promise((res, rej) => {
          const data = new FormData();
          data.append('upload', uploadedFile);
          async function upLoadImageBefore() {
            try {
              const res_wait = await apiClient.postMethod(URL_UPLOAD_IMAGE, data, null);
              return res_wait;
            } catch (e) {
              throw e;
            }
          }
          upLoadImageBefore().then((r) => {
            res({
              default: r.data.default,
            });
            rej(r.messageCode);
          });
        }),
    );
  }
}
```

> You should using Class Component in React for CKEditor beacause have issues if using function component
```js
import React, { Component } from 'react';

class EditorComponent extends Component {
  render() {
    const { value, onChange } = this.props; 
    // ...
    return (
      <CKEditor
        //...
      />
    );
  }
}
```

### License

- [CKEditor Ecosystem Documentation](https://ckeditor.com/docs/ckeditor5/latest/)
- [Custom image upload adapter](https://ckeditor.com/docs/ckeditor5/latest/framework/guides/deep-dive/upload-adapter.html)
