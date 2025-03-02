# Connie7777-star.github.io
<!DOCTYPE html>
<html>
<head>
    <title>多人协作编辑器</title>
    <!-- Firebase SDK -->
    <script src="https://www.gstatic.com/firebasejs/8.10.0/firebase-app.js"></script>
    <script src="https://www.gstatic.com/firebasejs/8.10.0/firebase-database.js"></script>
    
    <style>
        body {
            max-width: 800px;
            margin: 20px auto;
            padding: 20px;
            font-family: Arial, sans-serif;
        }
        #editor {
            width: 100%;
            height: 400px;
            border: 2px solid #3498db;
            padding: 15px;
            font-size: 16px;
            border-radius: 8px;
        }
        .user-count {
            color: #666;
            margin: 10px 0;
        }
    </style>
</head>
<body>
    <h2>多人协作编辑器 ✍️</h2>
    <div class="user-count">在线用户：<span id="users">0</span></div>
    <textarea id="editor" placeholder="开始输入..."></textarea>

    <script>
        // Firebase配置（需替换为您的项目配置）
        const firebaseConfig = {
            apiKey: "YOUR_API_KEY",
            authDomain: "YOUR_PROJECT_ID.firebaseapp.com",
            databaseURL: "https://YOUR_PROJECT_ID.firebaseio.com",
            projectId: "YOUR_PROJECT_ID",
            storageBucket: "YOUR_PROJECT_ID.appspot.com",
            messagingSenderId: "YOUR_SENDER_ID"
        };

        // 初始化Firebase
        firebase.initializeApp(firebaseConfig);
        const database = firebase.database();

        // 实时同步逻辑
        const editor = document.getElementById('editor');
        const usersSpan = document.getElementById('users');
        
        // 监听文档变化
        database.ref('documents/room1').on('value', (snapshot) => {
            editor.value = snapshot.val().content || '';
        });

        // 监听用户数量变化
        database.ref('users/room1').on('value', (snapshot) => {
            usersSpan.textContent = snapshot.numChildren();
        });

        // 用户输入时同步到数据库
        editor.addEventListener('input', (e) => {
            database.ref('documents/room1').update({
                content: e.target.value
            });
        });

        // 用户连接管理
        const userId = Date.now().toString(); // 生成唯一用户ID
        const userRef = database.ref('users/room1').child(userId);
        
        userRef.set(true);
        window.onbeforeunload = () => userRef.remove();
    </script>
</body>
</html>
