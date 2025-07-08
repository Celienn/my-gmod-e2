# Introduction
This is a collection of Expression 2 scripts I created and wanted to share.  
To use them, you will need to have both **Garry's Mod** and the **Wiremod** addon installed.

# Holographic Spider
Using the FABRIK inverse kinematics algorithm, I created a fully controllable holographic spider that can climb any surface.

todo add a short showcase video

![image](https://github.com/user-attachments/assets/eaa3415b-5db3-4053-979a-ed5caf6fe926)

### How to use:
- Wire the E2 to both a **Pod Controller** and a **Camera Controller**.
- Use the **WASD** keys to control the spider’s movement.

# Audio Vizualizer
![image](https://github.com/user-attachments/assets/fb5c1869-340f-4958-8b16-93402f0b9c8b)

todo add a short showcase video

## How to use 
It’s a bit tricky to get it working right now. You need to set up a web server that returns data like this. The easiest way is to use Node.js with Express, then make an API call to GitHub.
( or use soundcloud )

### Exemple of output
The URLs needs to be separated by a semicolon .
```
https://raw.githubusercontent.com/Celienn/my-gmod-e2/main/music/outer-wilds-main-title.mp3;https://raw.githubusercontent.com/Celienn/my-gmod-e2/main/music/outerwilds-travelers.mp3
```
### Node.js exemple
```javascript
const express = require('express');
const app = express();
const port = 80;
const https = require('https');

const owner = 'OWNER';
const repo = 'REPO';
const branch = 'main'; 

const options = {
  hostname: 'api.github.com',
  path: `/repos/${owner}/${repo}/git/trees/${branch}?recursive=1`,
  method: 'GET',
  headers: {
    'User-Agent': 'Node.js',
    'Accept': 'application/vnd.github.v3+json'
  }
};

let fileUrls = [];
https.get(options, (res) => {
  let data = '';

  res.on('data', (chunk) => { data += chunk; });
  res.on('end', () => {
    const tree = JSON.parse(data).tree;

    fileUrls = tree
      .filter(item => item.type === 'blob' && !item.path.endsWith('.md'))
      .map(item => `http://raw.githubusercontent.com/${owner}/${repo}/${branch}/${item.path}`);

    console.log('Filtered URLs:\n', fileUrls);
  });
}).on('error', (e) => {
  console.error(`Error: ${e.message}`);
});

app.get('/music', (req, res) => {
  res.send(fileUrls.join(';'));
});

app.listen(port, () => {
  console.log(`Server listening on port ${port}.`);
});
```
