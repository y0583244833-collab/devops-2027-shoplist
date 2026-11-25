# שיעור 6 — תשובות

## 1. למה `shoplist-backend:local` ולא `docker-backend`? ומה בלי `imagePullPolicy: Never`?
את השם `docker-backend` Compose המציא לפי שם התיקייה, והוא לא בשליטתנו. רצינו שם ותג קבועים שנבחר בעצמנו, שיהיו זהים בדיוק לשם שכתוב ב-`image:` בקובץ ה-YAML.
בלי `Never`, אם ה-image לא נמצא במחשב, Kubernetes ינסה להוריד אותו מ-Docker Hub, לא ימצא, וה-pod ייתקע ב-`ErrImagePull`. עם התג `latest` הוא היה מנסה להוריד תמיד.

## 2. פענוח `POSTGRES_PASSWORD`
הפקודה:
`[Text.Encoding]::UTF8.GetString([Convert]::FromBase64String("c2hvcHBhc3M="))`
והתוצאה: `shoppass`. base64 הוא קידוד ולא הצפנה: כל מי שיכולה לקרוא את ה-Secret מפענחת אותו בפקודה אחת. ההגנה האמיתית היא הרשאות (RBAC) ומי רשאית לקרוא Secrets, והצפנה של etcd.

## 3. איזה קובץ אחראי לכך שהמוצרים שרדו?
`postgres-pvc.yml`. ה-PVC נשאר גם כשה-pod נמחק, וה-pod החדש מתחבר לאותה ארונית.
הפקודה שכן הייתה מוחקת אותם: `kubectl delete pvc postgres-pvc -n shoplist`.

## 4. אין `depends_on`. מה קורה אם backend עולה לפני postgres?
ה-backend לא מצליח להתחבר, וה-readinessProbe שלו (`/health`) נכשל, או שהוא קורס ו-Kubernetes מרים אותו שוב. כל עוד הבדיקה נכשלת, ה-pod מסומן `0/1` ולא נכנס ל-endpoints, ולכן ה-Service לא שולח אליו בקשות. כש-postgres מוכן, הבדיקה מצליחה וה-pod מתחיל לקבל בקשות.

## 5. ד8: מה ראיתן, ולמה?
בדפדפן הנורית של backend הפכה לאדומה, והמוצרים לא נטענו. ה-pods של backend לא נפלו: הם נשארו `1/1 Running`. ב-`get endpoints backend` היה `<none>`.
הסיבה: ה-Service מחפש pods לפי ה-selector `app: backnd`, ואין אף pod עם המדבקה הזו (המדבקה היא `app: backend`). לכן אין ל-Service אף כתובת לשלוח אליה בקשות, למרות שה-pods תקינים.

## 6. שיניתי ב-`app-config`, אבל backend רואה את הערך הישן
משתני סביבה נקראים רק כשה-pod עולה. שינוי ב-ConfigMap לא משנה pods שכבר רצים. הפתרון: `kubectl rollout restart deployment/backend -n shoplist`, שמרים pods חדשים שיקראו את הערך החדש.

## 7. כמה פעמים כתבתי `shoplist`? ומה עם `shoplist-test`?
כתבתי `shoplist` 21 פעמים (בלי הערות): `namespace: shoplist` כמעט בכל קובץ, `name: shoplist` ב-`namespace.yml`, `-d shoplist` ב-`pg_isready`, ובשמות ה-images. כדי להריץ עותק ב-`shoplist-test` צריך לשנות ידנית את ה-namespace בכל 13 הקבצים. בשיעור הבא נפתור את זה עם Helm: כותבים את הערך פעם אחת, כמשתנה, ומשתמשים בו בכל הקבצים.