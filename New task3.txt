<html>
<head> 
 <title> Registration Form </title>
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
email VARCHAR(50) NOT NULL UNIQUE,
picname VARCHAR(50) NOT NULL UNIQUE,
picture LONGBLOB)";

if (mysqli_query($conn, $sqla)) {
    echo "Table MyUsers created successfully <br>";
} 

$uname = $email = $pword = $phoneno=$checkuname=$imageName=$image="";
$unameErr = $emailErr = $phonenoErr = $pwordErr =$imageErr="";

if ($_SERVER["REQUEST_METHOD"] == "POST") {
 
  if (empty($_POST["uname"])) 
      $unameErr = "Name is required";  
  else{$uname = mysqli_real_escape_string($conn,$_POST["uname"]);
       if (!preg_match("/^[a-zA-Z ]*$/",$uname)) 
       $nameErr = "Only letters and white space allowed"; 
      }
  
  if (empty($_POST["email"])) 
    $emailErr = "Email is required";
  else {$email = mysqli_real_escape_string($conn,$_POST["email"]);
       if (!filter_var($email, FILTER_VALIDATE_EMAIL)) 
       $emailErr = "Invalid email format";
       }

  if (empty($_POST["pword"])) 
    $pwordErr = "Password is required";  
  else {$pword = mysqli_real_escape_string($conn,$_POST["pword"]);
        if (!preg_match("/^[a-zA-Z0-9]*$/",$pword)) 
        $pwordErr = "Only letters and numbers allowed"; 
       }

  if (empty($_POST["phoneno"])) 
    $phonenoErr = "Phoneno is required";
  else {$phoneno = mysqli_real_escape_string($conn,$_POST["phoneno"]);
        if (!preg_match("/^[0-9]*$/",$phoneno)) 
        $phonenoErr = "Enter 10 digit number"; 
       }
  $checkuname=mysqli_real_escape_string($conn,$_POST["unames"]);
  $checkpword=mysqli_real_escape_string($conn,$_POST["pwords"]);

  $file=$_FILES['image']['tmp_name'];
  if(!isset($file))
    echo "please select image";
  else
    $image=addslashes(file_get_contents($_FILES['image']['tmp_name']));
    $imagename=addslashes($_FILES['image']['name']);
 
}

$hash=password_hash($pword, PASSWORD_DEFAULT);
$sqlb = "INSERT INTO MyUsers (uname,pword,email,phoneno,picname,picture)
VALUES ('$uname','$hash','$email','$phoneno','$imagename','$image')";

if (mysqli_query($conn, $sqlb) && $uname!="") 
    echo "You are Registered<br>";

if(password_verify('$checkpword',$hash))
{
$sqlc = "SELECT * FROM MyUsers WHERE uname='$checkuname'";
$row=mysqli_fetch_assoc(mysqli_query($conn, $sqlc));
if($row && $checkuname!=" ")
echo "Your details:<br><br>Username:".$row["uname"]."<br>E-mailID:".$row["email"]."<br>Phone no:".$row["phoneno"]."<br> Image:";
echo "<br>";?><img src="<?php echo $row["picname"]; ?>" height="100" width="100"><?php echo "<br>";
}
else "Wrong password";
mysqli_close($conn);

?>

<h2>Registration Form</h2>
<h4> Yet to Register ?? </h4>
<p><span class="error">* required</span></p>
<form method="post" action="<?php echo htmlspecialchars($_SERVER["PHP_SELF"]);?>" enctype="multipart/form-data">  
  Username : <input type="text" name="uname"> <span class="error">* <?php echo $unameErr;?> </span>
  <br><br>
  Passsword: <input type="password" name="pword"> <span class="error">* <?php echo $pwordErr;?> </span>
  <br><br>
  E-mail id: <input type="email" name="email"> <span class="error">* <?php echo $emailErr;?> </span>
  <br><br>
  Phone-no : <input type="tel" name="phoneno"> <span class="error">* <?php echo $phonenoErr;?> </span>
  <br><br>
  Image : <input type="file" name="image"> <span class="error">* <?php echo $imageErr;?> </span>
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





















