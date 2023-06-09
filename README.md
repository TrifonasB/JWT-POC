# JWT-POC

To JWT είναι ένα encoded token που γίνεται issue από τον server κατά το login ενός client και στη συνέχεια συνοδεύει τα αιτήματα του client ως http header για να παρέχει authentication και authorization. Κάθε JWT αποτελείται από 3 μέρη τα οποία κρυπτογραφούνται με Base64Url και χωρίζονται με τον χαρακτήρα '.' για εύκολο distinction. Το 1ο μέρος, το JWT header, αναγράφει τον αλγόριθμο κρυπτογράφησης και το είδος του token. Το 2ο μέρος, το payload, περιγράφει σε JSON format τα claims που έχει η επικοινωνία, είτε αφορούν τον client είτε άλλες πληροφορίες. Υπάρχουν κάποια predefined claims που χρησιμοποιούνται και υποτηρίζονται από libraries (βλέπε https://datatracker.ietf.org/doc/html/rfc7519#section-4.1) αλλά μπορούν αν οριστούν και οποιαδήποτε άλλα claims θέλουμε. Το 3ο μέρος, η υπογραφή, σχηματίζεται από την ακόλουθη μέθοδο: algorithm_specified_on_header(base64UrlEncode(header) + "." + base64UrlEncode(payload), secret_key). Οταν ο server λαβαίνει ένα νέο request κάνει decode το jwt και verify για να το συγκρίνει με την υπογραφή ώστε να βεβαιωθεί ότι δεν έχει αλλοιωθεί το payload.

## Πλεονεκτήματα JWT:
- λόγω του ότι ακολουθεί JSON format, είναι compact και δίνει μεγάλη ευκολία στην διαχείρηση καθώς το JSON κάνει map απευθείας σε object και υποστηρίζεται απο τι περισσότερες γλώσσες.
- η υπογραφή με ένα κοινό secret key κάνει την διαδικασία της κρυπτογράφησης απλή και ασφαλή.
- ο client δεν κάνει τίποτα πέρα από το να αναμεταδίδει το token που του έγινε issue.
- ο server δεν χρειάζεται να κάνει lookups καθώς όλη η πληροφορία εμεπριέχεται στο token.
- δεν μπορεί να αλλοιωθεί το token καθώς είναι signed και ο server πάντα ελέγχει το validity.
- οι χρήστες μπορούν να συνδεθούν με το ίδιο JWT σε οποιονδήποτε server έχει το ίδιο secret.
## Μειονεκτηματα JWT:
- Non-revocable. δεν μπορείς να ακυρώσεις ένα token πριν γίνει expire σε περίπτωση που θες να κάεις άμεσα ban κάποιον χρήστη.
- Βασίζεται στην μυστικότητα του κλειδιού. Αν το secret γίνει compromise τότε καταρρέει.
- Για JWT security issues βλέπε [εδώ](https://portswigger.net/web-security/jwt)

##JWT & .NET:
υπάρχουν πολλές βιβλιοθήκες που υποστηρίζουν την χρήση JWT σε .NET (https://jwt.io/libraries?language=.NET)


## Security Notes
αν κάποιος πάρει το secret key μπορεί να δημιουργήσει jwt με οποιαδήποτε claims θέλει.
αν δεν έχει το secret key και προσπαθήσει να κάνει κάοια αλλαγή θα σκασει το signature
αν καταφέρει να πάρει ένα valid jwt τότε μπορεί να έχει την ίδια ακριβώς πρόσβαση με τον χρήστη. good practice solution το secure transmission (TLS) και το short-expiration. υπάρχουν και τεχνικές όπως το token binding και το wrap σε HTTPOnly Cookie μεταξύ άλλων, για πιο ευαίσθητες εφαρμογές
το short expiration σημαίνει ότι ο χρήστης θα πρέπει να λάβει νέο jwt για να συνεχίσει. είτε κάνει ξανά log in είτε εφαρμόζεται silent token renewal

## demo info

### project
ASP.NET 6

### how to run
με Postman σε localhost χτυπάμε το login για να πάρουμε το JWT.
βαζουμε το JWT σε BearerToken στο Auth του Postman Request.
για να περάσω ορίσματα στο body raw JSON που να ταιριάζει με το DTO που περιμένει το endpoint.
στον UserController υπάρχουν διαφορα authorized actions.
