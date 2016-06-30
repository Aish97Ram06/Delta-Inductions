<html>
<head> 
<style>
.error {color: #FF0000;}
</style>
</head>
<body>

<?php
$servername = "localhost";
$username = "root";
$password = "";

$conn = mysqli_connect($servername, $username, $password);

$sql = "CREATE DATABASE myDB";
if (mysqli_query($conn, $sql)) {
    echo "Database created successfully <br>";
} 

$dbname="mydb";
$conn = mysqli_connect($servername, $username, $password,$dbname);

$sqla = "CREATE TABLE MyUsers (
phoneno VARCHAR(10) NOT NULL UNIQUE,
uname VARCHAR(30) NOT NULL UNIQUE,
pword VARCHAR(10) NOT NULL UNIQUE,
email VARCHAR(50) NOT NULL UNIQUE)";

if (mysqli_query($conn, $sqla)) {
    echo "Table MyUsers created successfully <br>";
} 

$uname = $email = $pword = $phoneno=$checkuname="";
$unameErr = $emailErr = $phonenoErr = $pwordErr = "";

if ($_SERVER["REQUEST_METHOD"] == "POST") {
 
  if (empty($_POST["uname"])) 
      $unameErr = "Name is required";  
  else{$uname = test_input($_POST["uname"]);
       if (!preg_match("/^[a-zA-Z ]*$/",$uname)) 
       $nameErr = "Only letters and white space allowed"; 
      }
  
  if (empty($_POST["email"])) 
    $emailErr = "Email is required";
  else {$email = test_input($_POST["email"]);
       if (!filter_var($email, FILTER_VALIDATE_EMAIL)) 
       $emailErr = "Invalid email format";
       }

  if (empty($_POST["pword"])) 
    $pwordErr = "Password is required";  
  else {$pword = test_input($_POST["pword"]);
        if (!preg_match("/^[a-zA-Z0-9]*$/",$pword)) 
        $pwordErr = "Only letters and numbers allowed"; 
       }

  if (empty($_POST["phoneno"])) 
    $phonenoErr = "Phoneno is required";
  else {$phoneno = test_input($_POST["phoneno"]);
        if (!preg_match("/^[0-9]*$/",$phoneno)) 
        $phonenoErr = "Enter 10 digit number"; 
       }
  
 $checkuname=test_input($_POST["unames"]);    }

function test_input($data) {
  $data = trim($data);
  $data = stripslashes($data);
  $data = htmlspecialchars($data);
  return $data;
}


$hash=password_hash($pword, PASSWORD_DEFAULT);
$sqlb = "INSERT INTO MyUsers (uname,pword,email,phoneno)
VALUES ('$uname','$hash','$email','$phoneno')";

if (mysqli_query($conn, $sqlb) && $uname!="") 
    echo "You are Registered";
if(password_verify($_POST["pwords"],'$hash'))
{
$sqlc = "SELECT * FROM MyUsers WHERE uname='$checkuname'";
$row=mysqli_fetch_assoc(mysqli_query($conn, $sqlc));
if($row && $checkuname!=" ")
echo "Your details:<br><br>Username: " . $row["uname"]."<br>E-mailID : " . $row["email"]."<br>Phone no: " . $row["phoneno"]."<br>";
}
else echo"wrong password";
mysqli_close($conn);

?>

<h2>Registration Form</h2>
<h4> Yet to Register ?? </h4>
<p><span class="error">* required</span></p>
<form method="post" action="<?php echo htmlspecialchars($_SERVER["PHP_SELF"]);?>">  
  Username : <input type="text" name="uname"> <span class="error">* <?php echo $unameErr;?> </span>
  <br><br>
  Passsword: <input type="password" name="pword"> <span class="error">* <?php echo $pwordErr;?> </span>
  <br><br>
  E-mail id: <input type="email" name="email"> <span class="error">* <?php echo $emailErr;?> </span>
  <br><br>
  Phone-no : <input type="tel" name="phoneno"> <span class="error">* <?php echo $phonenoErr;?> </span>
  <br><br>
  <input type="submit" name="submit" value="Submit">  
  <br><br>

<h4>Already registered ?? Login here </h4>
  Username : <input type="text" name="unames">
  <br><br>
  Passsword: <input type="password" name="pwords">
  <br><br>
  <input type="submit" name="submit" value="Submit">  
</form>
</body>
</html>