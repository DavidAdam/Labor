# Labor 12 - Launcher++

## Bevezet�

A labor c�lja az 5. laboron elk�sz�tett home screen alkalmaz�s kib�v�t�se.

A kiindul� projekt let�lthet� innen: [Kiindul� projekt](./assets/LauncherLabor_uptodate.zip) 

Az �t�s el�r�s�hez a minimum k�vetelm�nyeken t�l egy tov�bbi, szabadon v�laszott funkci� be�p�t�se sz�ks�ges. Az alkalmaz�sok elk�sz�t�s�hez �rdemes az �ra anyag�t �s a p�ldak�dokat alapul venni az android.telephony csomag haszn�lata sor�n.
[Telef�nia P�lda](./assets/TelefoniaPelda.zip) 

K�t n�zettel kell kib�v�teni az alkalmaz�st (a ViewPager-t). A k�t �j n�zettel szemben t�masztott k�vetelm�nyeket az al�bbi fejezetek tartalmazz�k.

## H�v�snapl�

A feladat egy h�v�snapl� n�zet megval�s�t�sa.

A n�zet legyen k�pes megjelen�teni a r�szletes h�v�snapl�t, k�l�n jelezve az egyes h�v�s t�pusokat (bej�v�, kimen�, nem fogadott)
Extra feladatok lehetnek p�ld�ul:

    A h�v�snapl� ne csak a telefonsz�mokat mutassa, hanem nevet, k�pet is, amennyiben a n�vjegyz�kben megtal�lhat�
    H�v�snapl� sz�r�se, rendez�se

## Kedvencek n�zet

Egy egyszer� lista n�zet rajta egy hozz�ad�s gombbal. A hozz�ad�s gombra kattintva ki lehet v�lasztani egy contact-ot, majd sikeres kiv�laszt�s ut�n beker�l a list�ba a contact a telefonsz�m�val egy�tt. Az adott elem jobb oldal�n legyen egy h�v�s gomb, amire r�kattintva fel lehet h�vni az adott telefonsz�mot.

### P�lda k�d a contact kiv�laszt�s�hoz

Kontakt v�laszt� feldob�sa:

```java	
Intent intent = new Intent(Intent.ACTION_PICK, ContactsContract.Contacts.CONTENT_URI);
startActivityForResult(intent, REQUEST_PICK_CONTACT);
```

Eredm�ny lekezel�se:

```java		
@Override
protected void onActivityResult(int requestCode, int resultCode, Intent data) {
    super.onActivityResult(requestCode, resultCode, data);
    if (resultCode == RESULT_OK) {
        switch (requestCode) {
            case REQUEST_PICK_CONTACT:
                Uri contactUri = data.getData();
                if (contactUri != null) {
                    Cursor c = null;
                    Cursor addrCur = null;
                    try {
                        ContentResolver cr = getContentResolver();
                        c = cr.query(contactUri,null,null, null, null);
                        if (c != null && c.moveToFirst()) {
                            String id = c.getString(c.getColumnIndex(BaseColumns._ID));
                            String name = c.getString(c.getColumnIndex(ContactsContract.Contacts.DISPLAY_NAME));
                            Log.d("Launcher", "name: " + name);
                            String contactNumber = null;
                            Cursor cursorPhone = getContentResolver().query(ContactsContract.CommonDataKinds.Phone.CONTENT_URI,
                                    new String[]{ContactsContract.CommonDataKinds.Phone.NUMBER},
 
                                    ContactsContract.CommonDataKinds.Phone.CONTACT_ID + " = ? AND " +
                                            ContactsContract.CommonDataKinds.Phone.TYPE + " = " +
                                            ContactsContract.CommonDataKinds.Phone.TYPE_MOBILE,
 
                                    new String[]{id},
                                    null);
 
                            if (cursorPhone.moveToFirst()) {
                                contactNumber = cursorPhone.getString(cursorPhone.getColumnIndex(ContactsContract.CommonDataKinds.Phone.NUMBER));
                            }
                            Log.d("Launcher", "contactNumber: " + contactNumber);
                            cursorPhone.close();
                        }
                    } finally {
                        if (c != null) {
                            c.close();
                        }
                        if (addrCur != null) {
                            addrCur.close();
                        }
                    }
                }
 
                break;
            default:
                break;
        }
    }
}
```

A p�lda k�d lek�rdezi a kiv�lasztott contact-ot �s megkeresi hozz� azt a telefonsz�m�t, ami mobilk�nt van megjel�lve.

## Felt�ltend� anyag

Az AUT port�lra felt�ltend� az alkalmaz�s projekt k�nyvt�r�n fel�l egy 2-3 oldalas felhaszn�l�i k�zik�nyv, ami le�rja az elk�sz�tett szoftver funkci�it, �s k�peket is tartalmaz minden relev�ns k�perny�r�l. A felhaszn�l�i k�zik�nyv is k�telez� r�sze a feladatnak, n�lk�le az anyag nem �rt�kelhet�!
A felt�lt�s hat�rideje: vas�rnap 23:59