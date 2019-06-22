## Awesome SignUp Form

`index.html`

```html
<!DOCTYPE html>
<html lang="en" dir="ltr">
  <head>
    <meta charset="utf-8">
    <title></title>
    <link rel="stylesheet" href="style.css">
    <link href="https://fonts.googleapis.com/css?family=Permanent+Marker&display=swap" rel="stylesheet">
  </head>
  <body>
    <div class="signup-form">
      <form class="" action="index.html" method="post">
        <h1>Sign Up</h1>
          <input type="text" placeholder="Full Name" class="txtb">
          <input type="password" placeholder="Password" class="txtb">
          <input type="email" placeholder="Email" class="txtb">
          <input type="submit" value="Create Account" class="signup-btn">
          <a href="#">Already Have one ?</a>
      </form>
    </div>
  </body>
</html>
```

<br>

`style.css`

```css
body
{
  margin: 0;
  padding: 0;
  background: #487eb0;
}
.signup-form
{
  width: 300px;
  padding: 20px;
  text-align: center;
  background: url(bg.png);
  background-repeat: no-repeat;
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  overflow: hidden;
}
.signup-form h1
{
  margin-top: 100px;
  font-family: 'Permanent Marker', cursive;
  color: #fff;
  font-size: 40px;
}
.signup-form input
{
  display: block;
  width: 100%;
  padding: 0 16px;
  height: 44px;
  text-align: center;
  box-sizing: border-box;
  outline: none;
  border: none;
  font-family: "montserrat", sans-serif;
}
.txtb
{
  margin: 20px 0;
  background: rgba(255, 255, 255, .5);
  border-radius: 6px;
}
.signup-btn
{
  margin-top: 60px;
  margin-bottom: 20px;
  background: #487eb0;
  color: #fff;
  border-radius: 44px;
  cursor: pointer;
  transition: 0.8s;
}
.signup-btn:hover
{
  transform: scale(0.96);
}
.signup-form a
{
  text-decoration: none;
  color: #fff;
  font-family: "montserrat", sans-serif;
  font-size: 14px;
  padding: 10px;
  transition: 0.8s;
  display: block;
}
.signup-form a:hover
{
  background: rgba(0, 0, 0, .3);
}
```

![Awesome SignUp Form](https://github.com/HedgehogKUCC/Self-Study/blob/master/HTML%26CSS/Awesome-SingUp-Form/AwesomeSignUpForm.jpg)

<br>
