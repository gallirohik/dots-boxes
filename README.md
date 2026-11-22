# Dots & Boxes: How well I want to know you
 
A two-player dots and boxes game (4 to 7 dots per side). The winner asks the loser one question, and every answer is saved for both players.
It is one static `index.html`, hosted on GitHub Pages. Moves sync through Firebase Realtime Database (free tier). No login is needed.
 
## 1. Create the Firebase database (about 5 minutes)
 
1. Go to https://console.firebase.google.com and click **Add project** (Google Analytics is not needed).
2. In the left menu, open **Build → Realtime Database → Create database**. Pick a location (for India, `asia-southeast1` is closest) and start in **locked mode**.
3. Open the **Rules** tab, paste the rules below, and click **Publish**:
```json
{
  "rules": {
    "games": {
      "$code": {
        ".read": true,
        ".write": true,
        ".validate": "$code.matches(/^[a-z0-9]{3,6}$/)",
        "state":   { ".validate": "newData.child('n').val() >= 3 && newData.child('n').val() <= 6" },
        "players": { "$seat": { ".validate": "$seat == '1' || $seat == '2'" } },
        "react":   { ".validate": "newData.child('e').isString() && newData.child('e').val().length <= 16" },
        "reactlog": { "$g": { "$id": { ".validate": "newData.child('e').isString() && newData.child('e').val().length <= 16" } } },
        "results": { "$id": { ".validate": "newData.child('s1').isNumber() && newData.child('s2').isNumber()" } },
        "answers": { "$id": { ".validate": "newData.child('q').isString() && newData.child('q').val().length <= 140 && (!newData.child('a').exists() || newData.child('a').val().length <= 280)" } }
      }
    }
  }
}
```
 
4. Go to **Project settings (gear icon) → General → Your apps → Web (`</>`)**. Register an app with any nickname; hosting is not needed.
5. Copy the `firebaseConfig` values it shows you.
## 2. Add the config to the game
 
Open `index.html`, find `FIREBASE_CONFIG` near the top of the script, and fill it in:
 
```js
const FIREBASE_CONFIG = {
  apiKey: "AIza...",
  authDomain: "your-project.firebaseapp.com",
  databaseURL: "https://your-project-default-rtdb.asia-southeast1.firebasedatabase.app",
  projectId: "your-project",
  appId: "1:...:web:..."
};
```
 
These values are meant to be public. Anyone who opens the page can see them, and that's fine: the rules above control what can be read and written.
 
## 3. Put it on GitHub Pages
 
1. On GitHub, create a new public repository, for example `dots`.
2. Upload `index.html` (and this README) to the repository.
3. Open **Settings → Pages**. Set **Source** to *Deploy from a branch* and **Branch** to `main` with folder `/ (root)`, then save.
4. After a minute or so, the game is live at `https://gallirohik.github.io/dots/`.
## 4. Play
 
1. Open the link, type your name, and tap **Start a game**.
2. Tap **Copy invite link** and send it to her. On a phone, this opens the share sheet.
3. She opens the link, types her name, and taps **Join**. The game starts as soon as you're both in.
## Notes
 
- Anyone who has the game code can open that game. The code is 5 random letters, and there's no account system.
- Each game code keeps its own record of answers. Rematches with the same code keep adding to it.
- If someone refreshes or reconnects, they keep their seat, because each device remembers its player ID.
 
