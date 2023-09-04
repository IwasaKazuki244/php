# php

<?php
if ($_SERVER["REQUEST_METHOD"] == "POST" && isset($_POST["tweet"])) {
    $username = "ユーザー名"; // ここにユーザー名を設定します
    $tweet_text = $_POST["tweet"];

    // データベースに新しいツイートを挿入
    $conn = new mysqli("localhost", "ユーザー名", "パスワード", "twitter_clone");
    $query = "INSERT INTO tweets (username, tweet_text) VALUES (?, ?)";
    $stmt = $conn->prepare($query);
    $stmt->bind_param("ss", $username, $tweet_text);

    if ($stmt->execute()) {
        // ツイートが成功した場合の処理
        header("Location: index.php"); // ツイート後にページをリダイレクト
        exit();
    } else {
        echo "ツイートに失敗しました。";
    }

    $stmt->close();
    $conn->close();
}

?>


<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <link rel="stylesheet" href="styles.css">
    <title>Twitter風投稿サイト</title>
</head>
<body>
    <header>
        <h1>Twitter風投稿サイト</h1>
    </header>
    <a href="login.html">ログイン</a>
    <a href="signap.html">新規登録</a>
    <div class="container">
        <div class="post-form">
            <textarea id="post-text" placeholder="何かつぶやく..."></textarea>
            <button id="post-button">投稿</button>
        </div>
        <div id="post-feed" class="post-feed">
            <!-- 投稿がここに表示される -->
        </div>
    </div>
    <script src="script.js"></script>
    
</body>
</html>


#javascript

// 投稿を追加する関数
function addPost() {
    const postText = document.getElementById("post-text").value;
    const postFeed = document.getElementById("post-feed");

    if (postText.trim() !== "") {
        const postDiv = document.createElement("div");
        postDiv.classList.add("post");

        const usernameSpan = document.createElement("span");
        usernameSpan.classList.add("username");
        usernameSpan.textContent = "ユーザー名";

        const postContent = document.createElement("p");
        postContent.classList.add("post-content");
        postContent.textContent = postText;

        const timestampSpan = document.createElement("span");
        timestampSpan.classList.add("timestamp");
        timestampSpan.textContent = new Date().toLocaleString();

        postDiv.appendChild(usernameSpan);
        postDiv.appendChild(postContent);
        postDiv.appendChild(timestampSpan);

        postFeed.appendChild(postDiv);

        document.getElementById("post-text").value = "";
    }
}

// ボタンクリックで投稿を追加
document.getElementById("post-button").addEventListener("click", addPost);


const express = require('express');
const mongoose = require('mongoose');
const bodyParser = require('body-parser');
const session = require('express-session');
const bcrypt = require('bcrypt');

const app = express();

// MongoDBに接続
mongoose.connect('mongodb://localhost/twitter-clone', { useNewUrlParser: true, useUnifiedTopology: true });

// ユーザーモデルを定義
const User = mongoose.model('User', {
    username: String,
    password: String,
});

// Expressのミドルウェア設定
app.use(bodyParser.urlencoded({ extended: true }));
app.use(session({ secret: 'your-secret-key', resave: false, saveUninitialized: true }));

// ログインページを表示
app.get('/login', (req, res) => {
    res.sendFile(__dirname + '/login.html');
});

// ログイン処理
app.post('/login', async (req, res) => {
    const { username, password } = req.body;
    const user = await User.findOne({ username });

    if (!user) {
        return res.send('ユーザーが存在しません。');
    }

    if (await bcrypt.compare(password, user.password)) {
        req.session.userId = user._id;
        return res.redirect('/dashboard');
    }

    res.send('パスワードが正しくありません。');
});

// 新規会員登録ページを表示
app.get('/signup', (req, res) => {
    res.sendFile(__dirname + '/signup.html');
});

// 新規会員登録処理
app.post('/signup', async (req, res) => {
    const { username, password } = req.body;

    const hashedPassword = await bcrypt.hash(password, 10);

    const user = new User({
        username,
        password: hashedPassword,
    });

    await user.save();

    res.send('新規会員登録が完了しました。');
});

// ダッシュボードページを表示
app.get('/dashboard', (req, res) => {
    if (!req.session.userId) {
        return res.redirect('/login');
    }

    res.send('ダッシュボードにログインしました。');
});

// サーバーを起動
app.listen(3000, () => {
    console.log('Server is running on port 3000');
});



#css


/* 基本のスタイル */
body {
    font-family: Arial, sans-serif;
    background-color: #f0f0f0;
    margin: 0;
    padding: 0;
}

header {
    background-color: #00aced;
    color: #fff;
    padding: 10px;
    text-align: center;
}

.container {
    max-width: 800px;
    margin: 20px auto;
    background-color: #fff;
    padding: 20px;
    border: 1px solid #ddd;
    border-radius: 5px;
    box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
}

/* 投稿フォームのスタイル */
.post-form {
    margin-bottom: 20px;
}

textarea {
    width: 100%;
    padding: 10px;
    border: 1px solid #ddd;
    border-radius: 5px;
    resize: none;
}

button {
    background-color: #00aced;
    color: #fff;
    border: none;
    padding: 10px 20px;
    border-radius: 5px;
    cursor: pointer;
}

/* 投稿のスタイル */
.post {
    border: 1px solid #ddd;
    padding: 10px;
    margin-bottom: 10px;
    border-radius: 5px;
}

/* 投稿のスタイル（ユーザー名） */
.username {
    font-weight: bold;
}

/* 投稿のスタイル（投稿内容） */
.post-content {
    margin-top: 10px;
}

/* 投稿のスタイル（タイムスタンプ） */
.timestamp {
    color: #888;
    font-size: 12px;
}

body a {
    position: absolute;
    top: 0;
    right: 0;
}
