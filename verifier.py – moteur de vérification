import cv2, pytesseract, re, requests, json, os
from datetime import datetime
from pytesseract import Output
from pdf2image import convert_from_path

RPPS_URL = "https://base-donnees-publique.medicaments.gouv.fr/api/rpps/{}"
HEADERS = {'User-Agent': 'OrdoVerif/1.0'}

def extract_text(image_path):
    img = cv2.imread(image_path)
    data = pytesseract.image_to_data(img, output_type=Output.DICT, lang='fra')
    text = " ".join(data["text"])
    return text

def find_rpps(text):
    match = re.search(r'\b(\d{11})\b', text)
    return match.group(1) if match else None

def check_rpps(rpps):
    try:
        r = requests.get(RPPS_URL.format(rpps), headers=HEADERS, timeout=5)
        if r.status_code == 200:
            data = r.json()
            return data.get("nom"), True
    except Exception as e:
        print("RPPS error", e)
    return None, False

def check_date(text):
    match = re.search(r'\b(\d{2})[/-](\d{2})[/-](\d{4})\b', text)
    if not match:
        return False, "Date absente"
    try:
        d = datetime.strptime(match.group(0), "%d/%m/%Y")
        if d > datetime.now():
            return False, "Date post-datée"
        if (datetime.now() - d).days > 365:
            return False, "Date périmée"
        return True, "Date OK"
    except:
        return False, "Date invalide"

def check_stamp(image_path):
    img = cv2.imread(image_path, 0)
    _, th = cv2.threshold(img, 0, 255, cv2.THRESH_BINARY + cv2.THRESH_OTSU)
    contours, _ = cv2.findContours(th, cv2.RETR_EXTERNAL, cv2.CHAIN_APPROX_SIMPLE)
    for cnt in contours:
        area = cv2.contourArea(cnt)
        if area > 500:
            (x, y), r = cv2.minEnclosingCircle(cnt)
            if 0.8 < area / (3.14 * r * r) < 1.2:
                return True
    return False

def verify(image_path):
    text = extract_text(image_path)
    rpps = find_rpps(text)
    nom_med, rpps_ok = check_rpps(rpps) if rpps else (None, False)
    date_ok, date_msg = check_date(text)
    stamp_ok = check_stamp(image_path)
    score = sum([rpps_ok, date_ok, stamp_ok])
    verdict = "VRAI" if score >= 2 else "FAUX"
    return {
        "verdict": verdict,
        "rpps": rpps,
        "nom_medecin": nom_med,
        "rpps_valid": rpps_ok,
        "date_msg": date_msg,
        "stamp_detected": stamp_ok,
        "score": f"{score}/3"
    }
