

*(link unavailable)*
```
from flask_sqlalchemy import SQLAlchemy

db = SQLAlchemy()

class Usuario(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    nombre = db.Column(db.String(100), nullable=False)
    email = db.Column(db.String(100), nullable=False, unique=True)
    password = db.Column(db.String(100), nullable=False)
    saldo = db.Column(db.Float, nullable=False, default=0.0)

class Movimiento(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    usuario_id = db.Column(db.Integer, db.ForeignKey('(link unavailable)'))
    monto = db.Column(db.Float, nullable=False)
    tipo = db.Column(db.String(100), nullable=False)  # "ingreso" o "egreso"
    fecha = db.Column(db.DateTime, nullable=False, default=db.func.current_timestamp())

class Producto(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    nombre = db.Column(db.String(100), nullable=False)
    precio = db.Column(db.Float, nullable=False)
```

*(link unavailable)*
```
from flask import Flask, render_template, request, redirect, url_for
from models import Usuario, Movimiento, Producto
from flask_login import LoginManager, UserMixin, login_required, login_user, logout_user

app = Flask(__name__)
app.config["SQLALCHEMY_DATABASE_URI"] = "sqlite:///database.db"
db.init_app(app)

login_manager = LoginManager()
login_manager.init_app(app)

@login_manager.user_loader
def load_user(user_id):
    return Usuario.query.get(int(user_id))

@app.route("/")
def index():
    return render_template("index.html")

@app.route("/login", methods=["POST"])
def login():
    email = request.form["email"]
    password = request.form["password"]
    usuario = Usuario.query.filter_by(email=email).first()
    if usuario and usuario.password == password:
        login_user(usuario)
        return redirect(url_for("perfil"))
    return "Email o contraseña incorrectos"

@app.route("/register", methods=["POST"])
def register():
    nombre = request.form["nombre"]
    email = request.form["email"]
    password = request.form["password"]
    usuario = Usuario(nombre=nombre, email=email, password=password)
    db.session.add(usuario)
    db.session.commit()
    return redirect(url_for("index"))

@app.route("/perfil")
@login_required
def perfil():
    movimientos = Movimiento.query.filter_by(usuario_id=(link unavailable)).all()
    return render_template("perfil.html", movimientos=movimientos)

@app.route("/agregar_producto", methods=["POST"])
@login_required
def agregar_producto():
    producto_id = request.form["producto_id"]
    cantidad = request.form["cantidad"]
    producto = Producto.query.get(producto_id)
    movimiento = Movimiento(usuario_id=(link unavailable), monto=producto.precio * cantidad, tipo="egreso")
    db.session.add(movimiento)
    db.session.commit()
    return redirect(url_for("perfil"))

@app.route("/saldo")
@login_required
def saldo():
    return str(current_user.saldo)
```

*templates/index.html*
```
<!DOCTYPE html>
<html>
<head>
    <title>Iniciar sesión o registrarse</title>
</head>
<body>
    <h1>Iniciar sesión</h1>
    <form action="/login" method="post">
        <label for="email">Email:</label>
        <input type="email" id="email" name="email"><br><br>
        <label for="password">Contraseña:</label>
        <input type="password" id="password" name="password"><br><br>
        <input type="submit" value="Iniciar sesión">
    </form>
    <h1>Registrarse</h1>
    <form action="/register" method="post">
        <label for="nombre">Nombre:</label>
        <input type="text" id="nombre" name="nombre"><br><br>
        <label for="email">Email:</label>
        <input type="email" id="email" name="email"><br><br>
        <label for="password">Contraseña:</label>
        <input type="password" id="password" name="password"><br><br>
        <input type="submit" value="Registrarse">
    </form>
</body>
</html>
```

*templates/perfil.html*
```
<!DOCTYPE html>
<html>
<head>
    <title>Perfil</title>
</head>
<body>
    <h1>Perfil de {{ current_user.nombre }}</h1>
    <p>Saldo: {{ current_user.saldo }}</p>
    <h2>Movimientos</h2>
    <ul>
        {% for movimiento in movimientos %}
```
