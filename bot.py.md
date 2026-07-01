from playwright.sync_api import sync_playwright
import requests
import os

URL = "https://ecommerce.lade.faa.mil.ar/flightresults/?module=ecommerce&no_tab=true"

TOKEN = os.environ["TELEGRAM_TOKEN"]
CHAT_ID = os.environ["CHAT_ID"]


def avisar():
    requests.post(
        f"https://api.telegram.org/bot{TOKEN}/sendMessage",
        data={
            "chat_id": CHAT_ID,
            "text": "🚨 LADE DISPONIBLE 🚨\n\nBHI ✈️ BRC\n09/07/2026\n\nEntrá a revisar ahora."
        }
    )


def revisar():

    with sync_playwright() as p:

        browser = p.chromium.launch()
        page = browser.new_page()

        page.goto(URL, wait_until="networkidle")

        texto = page.inner_text("body")

        print(texto)

        if (
            "Agotada" not in texto
            and "No hay lugares disponibles" not in texto
        ):
            avisar()

        browser.close()


revisar()