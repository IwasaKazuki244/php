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
