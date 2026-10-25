# בחירת Image בסיסי של Python בגרסה 3.12
FROM python:3.12-slim

# הגדרת תיקיית העבודה בתוך הקונטיינר
WORKDIR /app

# העתקת קובץ התלויות לתיקיית העבודה
COPY requirements.txt .

# התקנת הספריות הנדרשות ללא שמירת מטמון של pip
RUN pip install --no-cache-dir -r requirements.txt

# העתקת כל קובצי הפרויקט לתיקיית העבודה
COPY . .

# הצהרה שהאפליקציה מאזינה בפורט 8000
EXPOSE 8000

# הגדרת הפקודה שתפעיל את האפליקציה בעת הרצת הקונטיינר
CMD ["python", "app.py"]
