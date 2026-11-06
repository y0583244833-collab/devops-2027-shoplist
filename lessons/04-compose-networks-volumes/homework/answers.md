# תשובות — שיעור 4

1. שירות stats פונה לשרת דרך רשת Compose בכתובת http://backend:5000. הדפדפן פונה ל־localhost:8080, ו־Nginx מעביר את בקשות המוצרים לשרת.

2. אחרי docker compose down ואז up המוצרים נשמרים, כי PostgreSQL שומר אותם ב־named volume. הפקודה docker compose down -v מוחקת גם את ה־volume ואת הנתונים שבו.

3. זהו bind mount כי המקור הוא נתיב לקובץ במחשב, ../sql/init.sql. PostgreSQL מריץ אותו באתחול של מסד חדש, ולכן שינוי בקובץ אחרי יצירת המסד לא יעדכן אוטומטית את הטבלה הקיימת.

4. שם הרשת הוא docker_default ושם ה־volume הוא docker_postgres-data. הקידומת docker מגיעה משם תיקיית הפרויקט שבה נמצא קובץ ה־Compose, default הוא שם רשת ברירת המחדל ו־postgres-data הוא השם שהגדרנו ל־volume.

5. ה־image הסופי מכיל את Nginx, את קובץ ההגדרות ואת קובצי האתר שנבנו ב־dist, ואילו Node נמצא בשלב הבנייה בלבד. פקודת ההפעלה מגיעה מה־image של Nginx, ולשירות stats מספיק שלב אחד כי Node מריץ את server.js ישירות.

6. לפי DISK USAGE, הגודל של single הוא 92.7MB, של multi הוא 92.7MB ושל kitchen הוא 244MB. שני הראשונים מוצגים באותו גודל כי שניהם מבוססים על אותו Nginx וקובצי האתר קטנים, ואילו kitchen כולל את סביבת Node.

7. הבקשה /api/stats מגיעה ל־stats כי זו הקידומת הארוכה ביותר שמתאימה לבקשה. ב־proxy_pass של ה־backend הלוכסן הסופי מחליף את הקידומת /api/ ב־/, ולכן /api/products מגיע לשרת בתור /products.
