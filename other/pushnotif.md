MainActivity.java
package com.example.myapplication;
import static com.example.myapplication.R.layout.activity_main;
import android.os.Bundle;
import android.view.View;
import android.widget.Button;
import android.widget.EditText;
import android.widget.TextView;

import androidx.appcompat.app.AppCompatActivity;

public class MainActivity extends AppCompatActivity {

    public static int[] gcdExt(int a, int b) {
        /* To return array [gcd(a,b), x, y] such that ax + by = gcd(a,b) */
        if (b == 0) {
            return new int[]{a, 1, 0};
        }

        int[] vals = gcdExt(b, a % b);
//        vals[1] -= (a / b) * vals[2];
        int d = vals[0];
        int x = vals[2];
        int y = vals[1] - (a / b) * vals[2];

        return new int[]{d, x, y};
    }

    public static int inverse(int k, int n) {
        /* To compute and return modular multiplicative inverse if it exists, else return -1 */
        int[] vals = gcdExt(k, n);
        int d = vals[0];
        int a = vals[1];
//        int b = vals[2];
        if (d > 1) {
            return -1;
        }
        if (a > 0) {
            return a;
        }
        return n + a;
    }

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(activity_main);
        Button btn = findViewById(R.id.button);
        btn.setOnClickListener(v -> {
            EditText input = findViewById(R.id.editTextNumber);
            int num = Integer.parseInt(input.getText().toString());
            TextView txtView = findViewById(R.id.textView2);
            int res = inverse(num, 26);
            String out = "No inverse";
            if(res != -1){
                out = Integer.toString(res);
            }
            txtView.setVisibility(View.VISIBLE);
            txtView.setText(out);
        });

    }
}

activity_main.xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    <TextView
        android:id="@+id/textView"
        android:layout_width="126dp"
        android:layout_height="45dp"
        android:layout_marginTop="60dp"
        android:text="Inverse"
        android:textAppearance="@style/TextAppearance.AppCompat.Large"
        app:autoSizeTextType="uniform"
        app:fontFamily="sans-serif"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent"
        app:textAllCaps="true" />

    <Button
        android:id="@+id/button"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginBottom="56dp"
        android:text="Find Inverse mod 26"
        app:layout_constraintBottom_toTopOf="@+id/textView2"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintHorizontal_bias="0.497"
        app:layout_constraintStart_toStartOf="parent" />

    <TextView
        android:id="@+id/textView2"
        android:layout_width="104dp"
        android:layout_height="35dp"
        android:text="TextView"
        android:visibility="gone"
        app:autoSizeTextType="uniform"
        app:fontFamily="sans-serif-black"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintHorizontal_bias="0.508"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent"
        app:layout_constraintVertical_bias="0.542" />

    <EditText
        android:id="@+id/editTextNumber"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:ems="10"
        android:hint="Enter a number..."
        android:inputType="number"
        android:minHeight="48dp"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent"
        app:layout_constraintVertical_bias="0.25" />

</androidx.constraintlayout.widget.ConstraintLayout>
