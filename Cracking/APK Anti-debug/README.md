
# APK - Anti-debug
https://www.root-me.org/en/Challenges/Cracking/APK-Anti-debug
```
L'objectif est de trouver le mot de passe qui valide l'application Android.
```

Selon le fichier AndroidManifest.xml, il n'y a qu'une seule activité dans cette application (PuzzleActivity), qui est la principale (bien sûr).<br> Cette activité ne contient pas beaucoup de code :
```java
public class PuzzleActivity extends Activity {
    public EditText editTxt;
    public TextView txtView;
    public Validate valid;
    public Button validateBtn;

    class C00001 implements OnClickListener {
        C00001() {
        }

        public void onClick(View v) {
            PuzzleActivity.this.txtView.setText(Validate.checkSecret(PuzzleActivity.this.editTxt.getText().toString()));
            PuzzleActivity.this.editTxt.setEnabled(false);
            PuzzleActivity.this.validateBtn.setEnabled(false);
        }
    }

    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(C0001R.layout.main);
        this.validateBtn = (Button) findViewById(C0001R.id.validateButton);
        this.editTxt = (EditText) findViewById(C0001R.id.textArea);
        this.txtView = (TextView) findViewById(C0001R.id.textView1);
        this.valid = new Validate(getApplicationContext());
        this.validateBtn.setOnClickListener(new C00001());
    }
}
```
Comme vous pouvez le constater, dans la fonction *onClick*, il y a un appel à la fonction **Validate.checkSecret**, qui prend le mot de passe en entrée. Donc, le mot de passe devrait probablement être là (le nom le suggère) :
```java
public static String checkSecret(String input) {
    try {
        MessageDigest digest = MessageDigest.getInstance("SHA-256");
        digest.reset();
        byte[] computedHash = digest.digest(input.getBytes());
        if (!computed) {
            convert2bytes();
        }
        for (int i = 0; i < hashes.length; i++) {
            if (Arrays.equals(computedHash, bh[i])) {
                return answers[i];
            }
        }
    } catch (Exception exp) {
        Log.w("Hashdays", "checkSecret: " + exp.toString());
    }
    return answers[4];
}
```
Comme vous pouvez le voir, le mot de passe est haché (en utilisant le hachage SHA-256) et converti en chaîne hexadécimale.<br> Ensuite, il va comparer le hachage du mot de passe à chaque hachage stocké dans le tableau **hashes**.<br> Si le hachage est égal à l'un des hachages, alors il renverra une réponse du tableau answers, selon l'index du hachage (dans le tableau hashes).<br><br>

Les tableaux **hashes** et **answers** sont :
```java
    private static final String[] answers = new String[]{"Congrats from the FortiGuard team :)", "Nice try, but that would be too easy", "Ha! Ha! FortiGuard grin ;)", "Are you implying we are n00bs?", "Come on, this is a DEFCON conference!"};
    private static final String[] hashes = new String[]{"622a751d6d12b46ad74049cf50f2578b871ca9e9447a98b06c21a44604cab0b4", "301c4cd0097640bdbfe766b55924c0d5c5cc28b9f2bdab510e4eb7c442ca0c66", "d09e1fe7c97238c68e4be7b3cd64230c638dde1d08c656a1c9eaae30e49c4caf", "4813494d137e1631bba301d5acab6e7bb7aa74ce1185d456565ef51d737677b2"};
```

Il ne nous reste plus qu'à inverser le premier hachage dans **hashes**.<br>
