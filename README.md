# Task---2

# db.php
<?php
$conn = mysqli_connect("localhost", "root", "", "test_db");
if (!$conn) {
    die("Connection failed: " . mysqli_connect_error());
}
?>

# signup.php
<?php
include 'db.php';

if ($_SERVER["REQUEST_METHOD"] == "POST") {
    $user = $_POST['username'];
    $pass = password_hash($_POST['password'], PASSWORD_DEFAULT);

    // Check if username already exists
    $check = mysqli_prepare($conn, "SELECT id FROM users WHERE username = ?");
    mysqli_stmt_bind_param($check, "s", $user);
    mysqli_stmt_execute($check);
    mysqli_stmt_store_result($check);

    if (mysqli_stmt_num_rows($check) > 0) {
        echo "<p style='text-align:center; color: red;'>Username already taken. Please try another.</p>";
    } else {
        $stmt = mysqli_prepare($conn, "INSERT INTO users (username, password) VALUES (?, ?)");
        mysqli_stmt_bind_param($stmt, "ss", $user, $pass);
        mysqli_stmt_execute($stmt);
        echo "<p style='text-align:center; color: green;'>Signup successful. <a href='login.php'>Login here</a></p>";
    }
}
?>

<link rel="stylesheet" href="style.css">
<form method="POST">
    <h2>Sign Up</h2>
    <input name="username" placeholder="Username" required>
    <input type="password" name="password" placeholder="Password" required>
    <button type="submit">Sign Up</button>
</form>


# login.php
<?php
include 'db.php';
session_start();

if ($_SERVER["REQUEST_METHOD"] == "POST") {
    $user = $_POST['username'];
    $pass = $_POST['password'];

    $stmt = mysqli_prepare($conn, "SELECT id, password FROM users WHERE username = ?");
    mysqli_stmt_bind_param($stmt, "s", $user);
    mysqli_stmt_execute($stmt);
    mysqli_stmt_bind_result($stmt, $id, $hashed);
    mysqli_stmt_fetch($stmt);

    if (password_verify($pass, $hashed)) {
        $_SESSION['user_id'] = $id;
        header("Location: welcome.php");
        exit();
    } else {
        echo "<p style='text-align:center;color:red;'>Invalid credentials.</p>";
    }
}
?>

<link rel="stylesheet" href="style.css">
<form method="POST">
    <h2>Login</h2>
    <input name="username" placeholder="Username" required>
    <input type="password" name="password" placeholder="Password" required>
    <button type="submit">Login</button>
</form>


# welcome.php
<?php
session_start();
if (!isset($_SESSION['user_id'])) {
    header("Location: login.php");
    exit();
}
?>

<link rel="stylesheet" href="style.css">
<form>
    <h2>Welcome!</h2>
    <p>You are successfully logged in.</p>
    <a href="logout.php">Logout</a>
</form>

# logout.php
<?php
session_start();
session_destroy();
header("Location: login.php");
exit();
?>

# style.css
body {
    font-family: Arial, sans-serif;
    background: linear-gradient(to right, #83a4d4, #b6fbff);
    display: flex;
    justify-content: center;
    align-items: center;
    height: 100vh;
}

form {
    background-color: white;
    padding: 25px 30px;
    border-radius: 10px;
    box-shadow: 0px 0px 10px #888;
    width: 300px;
    text-align: center;
}

h2 {
    margin-bottom: 20px;
    color: #333;
}

input {
    width: 100%;
    padding: 10px;
    margin: 8px 0;
    box-sizing: border-box;
    border-radius: 5px;
    border: 1px solid #ccc;
}

button {
    background-color: #007BFF;
    color: white;
    padding: 10px 20px;
    margin-top: 10px;
    border: none;
    border-radius: 5px;
    cursor: pointer;
}

button:hover {
    background-color: #0056b3;
}

a {
    display: inline-block;
    margin-top: 10px;
    color: #007BFF;
    text-decoration: none;
}

a:hover {
    text-decoration: underline;
}
	
