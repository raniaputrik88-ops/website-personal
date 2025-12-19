# website-personal
web
from flask import Flask, render_template, request, jsonify
app = Flask(__name__)

@app.route("/")
def index():
    return render_template("index.html", items=ITEMS)
ITEMS = {
    "Nasi Goreng": 13000,
    "Mie Ayam": 12000,
    "Bakso": 10000,
    "Es Teh": 5000,
    "Es Jeruk": 7000,
    "Es Sirup": 8000
}

@app.route("/price/<code>")
def price(code):
    item = ITEMS.get(code.upper())
    if not item:
        return jsonify({"ok": False}), 404
    return jsonify({"ok": True, "name": item["name"], "price": item["price"]})

@app.route("/checkout", methods=["POST"])
def checkout():
    data = request.json
    cart = data.get("cart", [])
    total = 0
    lines = []
    for entry in cart:
        code = entry.get("code","").upper()
        qty = int(entry.get("qty",0))
        if code in ITEMS and qty>0:
            name = ITEMS[code]["name"]
            price = ITEMS[code]["price"]
            subtotal = price * qty
            total += subtotal
            lines.append({"code":code,"name":name,"price":price,"qty":qty,"subtotal":subtotal})
    bayar = int(data.get("bayar",0))
    kembalian = bayar - total if bayar >= total else None
    return jsonify({"ok": True, "lines": lines, "total": total, "bayar": bayar, "kembalian": kembalian})

if __name__ == "__main__":
    app.run(debug=True)

