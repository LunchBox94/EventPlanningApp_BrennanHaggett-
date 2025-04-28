# EventPlanningApp_BrennanHaggett-
Portfolio artifact for CS 360 - Event Planning App
[EventPlanningAPP_BrennanHaggett.zip](https://github.com/user-attachments/files/19935139/EventPlanningAPP_BrennanHaggett.zip)


# Activity_main.xml

<?xml version="1.0" encoding="utf-8"?>
<LinearLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:orientation="vertical"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:padding="16dp">

    <EditText
        android:id="@+id/usernameInput"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:hint="Username"
        tools:ignore="Autofill,HardcodedText,TextFields,TouchTargetSizeCheck,VisualLintTextFieldSize" />

    <EditText
        android:id="@+id/passwordInput"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:hint="Password"
        android:inputType="textPassword"
        tools:ignore="Autofill,HardcodedText,TouchTargetSizeCheck,VisualLintTextFieldSize" />

    <Button
        android:id="@+id/loginButton"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Login"
        tools:ignore="HardcodedText,VisualLintButtonSize" />

    <Button
        android:id="@+id/signupButton"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Sign Up"
        tools:ignore="HardcodedText,VisualLintButtonSize" />

</LinearLayout>



# Activity_login.xml

<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:background="@android:color/white"
    android:gravity="center_horizontal"
    android:orientation="vertical"
    android:padding="24dp">

    <!-- Username Input -->
    <EditText
        android:id="@+id/usernameField"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginBottom="16dp"
        android:autofillHints="username"
        android:hint="@string/username_hint"
        android:inputType="text"
        android:minHeight="48dp"
        tools:ignore="VisualLintTextFieldSize" />

    <!-- Password Input -->
    <EditText
        android:id="@+id/passwordField"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginBottom="24dp"
        android:autofillHints="password"
        android:hint="@string/password_hint"
        android:inputType="textPassword"
        android:minHeight="48dp"
        tools:ignore="VisualLintTextFieldSize" />

    <!-- Login Button -->
    <Button
        android:id="@+id/loginButton"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginBottom="12dp"
        android:text="@string/login_button_text"
        tools:ignore="VisualLintButtonSize" />

    <!-- Create Account Button -->
    <Button
        android:id="@+id/createAccountButton"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="@string/create_account_button_text"
        tools:ignore="VisualLintButtonSize" />
</LinearLayout>


# AndoidManifest.xml

<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android">

    <!-- Permissions and Features -->
    <uses-feature
        android:name="android.hardware.telephony"
        android:required="false" />
    <uses-permission android:name="android.permission.SEND_SMS" />

    <application
        android:allowBackup="true"
        android:dataExtractionRules="@xml/data_extraction_rules"
        android:fullBackupContent="@xml/backup_rules"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:roundIcon="@mipmap/ic_launcher_round"
        android:supportsRtl="true"
        android:theme="@style/Theme.EventPlanningAPP_BrennanHaggett">

        <!-- Main launcher activity -->
        <activity
            android:name=".MainActivity"
            android:exported="true">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>

        <!-- Activity to view list of events -->
        <activity android:name=".EventListActivity" />

        <!-- Activity to add new events -->
        <activity android:name=".AddEventActivity" />

        <!-- Future SMS feature activity, if applicable -->
        <!-- <activity android:name=".SmsNotificationActivity" /> -->

    </application>
</manifest>

# EventListActivity.java

package com.example.eventplanningapp_brennanhaggett;

import android.app.Activity;
import android.content.Intent;
import android.os.Bundle;
import android.widget.ArrayAdapter;
import android.widget.Button;
import android.widget.ListView;

import java.util.ArrayList;

/**
 * EventListActivity displays a list of all saved events from the database.
 * Users can view events and add new ones.
 */
public class EventListActivity extends Activity {

    private UserDatabaseHelper dbHelper;
    private ArrayList<String> eventList;
    private ArrayAdapter<String> adapter;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_event_list); // Layout for event list screen

        // Initialize database helper
        dbHelper = new UserDatabaseHelper(this);

        // Link views
        ListView eventListView = findViewById(R.id.eventListView);
        Button addEventButton = findViewById(R.id.addEventButton);

        // Load all events from database
        eventList = dbHelper.getAllEventStrings();
        adapter = new ArrayAdapter<>(this, android.R.layout.simple_list_item_1, eventList);
        eventListView.setAdapter(adapter);

        // Handle "Add Event" button click
        addEventButton.setOnClickListener(v -> {
            Intent intent = new Intent(EventListActivity.this, AddEventActivity.class);
            startActivity(intent);
        });
    }

    @Override
    protected void onResume() {
        super.onResume();
        // Refresh event list every time this screen comes back
        eventList.clear();
        eventList.addAll(dbHelper.getAllEventStrings());
        adapter.notifyDataSetChanged();
    }
}

# AddEventActivity.java

package com.example.eventplanningapp_brennanhaggett;

import android.app.Activity;
import android.os.Bundle;
import android.widget.Button;
import android.widget.EditText;
import android.widget.Toast;

public class AddEventActivity extends Activity {

    private EditText eventTitleInput, eventDateInput;
    private UserDatabaseHelper dbHelper;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_add_event); // Make sure file name matches

        // Initialize database helper
        dbHelper = new UserDatabaseHelper(this);

        // Link layout views
        eventTitleInput = findViewById(R.id.eventTitleInput);
        eventDateInput = findViewById(R.id.eventDateInput);
        Button saveEventButton = findViewById(R.id.saveEventButton);

        // Handle save event click
        saveEventButton.setOnClickListener(v -> {
            String title = eventTitleInput.getText().toString().trim();
            String date = eventDateInput.getText().toString().trim();

            if (!title.isEmpty() && !date.isEmpty()) {
                boolean success = dbHelper.addEvent(title, date);
                if (success) {
                    Toast.makeText(AddEventActivity.this, "Event saved!", Toast.LENGTH_SHORT).show();
                    finish(); // Close this activity and return
                } else {
                    Toast.makeText(AddEventActivity.this, "Error saving event.", Toast.LENGTH_SHORT).show();
                }
            } else {
                Toast.makeText(AddEventActivity.this, "Fill out both fields.", Toast.LENGTH_SHORT).show();
            }
        });
    }
}

# UserDatabaseHelper.java

package com.example.eventplanningapp_brennanhaggett;

import android.content.ContentValues;
import android.content.Context;
import android.database.Cursor;
import android.database.sqlite.SQLiteDatabase;
import android.database.sqlite.SQLiteOpenHelper;

import java.util.ArrayList;

/**
 * UserDatabaseHelper manages user accounts and event records.
 */
public class UserDatabaseHelper extends SQLiteOpenHelper {

    private static final String DATABASE_NAME = "events.db";
    private static final int DATABASE_VERSION = 1;

    // Events table
    public static final String TABLE_EVENTS = "events";
    public static final String COLUMN_ID = "_id";
    public static final String COLUMN_TITLE = "title";
    public static final String COLUMN_DATE = "date";

    // Users table
    public static final String TABLE_USERS = "users";
    public static final String COLUMN_USERNAME = "username";
    public static final String COLUMN_PASSWORD = "password";

    // Create Events table
    private static final String CREATE_EVENTS_TABLE = "CREATE TABLE " + TABLE_EVENTS + " (" +
            COLUMN_ID + " INTEGER PRIMARY KEY AUTOINCREMENT, " +
            COLUMN_TITLE + " TEXT, " +
            COLUMN_DATE + " TEXT);";

    // Create Users table
    private static final String CREATE_USERS_TABLE = "CREATE TABLE " + TABLE_USERS + " (" +
            COLUMN_USERNAME + " TEXT PRIMARY KEY, " +
            COLUMN_PASSWORD + " TEXT);";

    public UserDatabaseHelper(Context context) {
        super(context, DATABASE_NAME, null, DATABASE_VERSION);
    }

    @Override
    public void onCreate(SQLiteDatabase db) {
        db.execSQL(CREATE_EVENTS_TABLE);
        db.execSQL(CREATE_USERS_TABLE);
    }

    @Override
    public void onUpgrade(SQLiteDatabase db, int oldVersion, int newVersion) {
        db.execSQL("DROP TABLE IF EXISTS " + TABLE_EVENTS);
        db.execSQL("DROP TABLE IF EXISTS " + TABLE_USERS);
        onCreate(db);
    }

    // Add new event
    public boolean addEvent(String title, String date) {
        SQLiteDatabase db = this.getWritableDatabase();
        ContentValues values = new ContentValues();
        values.put(COLUMN_TITLE, title);
        values.put(COLUMN_DATE, date);

        long result = db.insert(TABLE_EVENTS, null, values);
        return result != -1;
    }

    // Get all events
    public ArrayList<String> getAllEventStrings() {
        ArrayList<String> eventList = new ArrayList<>();
        SQLiteDatabase db = this.getReadableDatabase();
        Cursor cursor = db.query(TABLE_EVENTS, null, null, null, null, null, null);

        if (cursor.moveToFirst()) {
            do {
                String title = cursor.getString(cursor.getColumnIndexOrThrow(COLUMN_TITLE));
                String date = cursor.getString(cursor.getColumnIndexOrThrow(COLUMN_DATE));
                eventList.add(title + " - " + date);
            } while (cursor.moveToNext());
        }
        cursor.close();
        return eventList;
    }

    // Check if user exists (for login)
    public boolean checkUser(String username, String password) {
        SQLiteDatabase db = this.getReadableDatabase();
        Cursor cursor = db.rawQuery(
                "SELECT * FROM " + TABLE_USERS + " WHERE " + COLUMN_USERNAME + " = ? AND " + COLUMN_PASSWORD + " = ?",
                new String[]{username, password}
        );
        boolean exists = (cursor.getCount() > 0);
        cursor.close();
        return exists;
    }

    // Register a new user
    public boolean registerUser(String username, String password) {
        SQLiteDatabase db = this.getWritableDatabase();
        ContentValues values = new ContentValues();
        values.put(COLUMN_USERNAME, username);
        values.put(COLUMN_PASSWORD, password);

        long result = db.insert(TABLE_USERS, null, values);
        return result != -1;
    }
}

# Activity_event_list.xml

<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:orientation="vertical"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:padding="16dp"
    android:background="@android:color/white"
    tools:context=".EventListActivity"
    tools:ignore="VisualLintBounds">

    <!-- Button to add a new event -->
    <Button
        android:id="@+id/addEventButton"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="@string/add_event"
        android:layout_marginBottom="16dp"
        android:backgroundTint="@color/purple_500"
        android:textColor="@android:color/white"
        android:contentDescription="@string/add_event"
        tools:ignore="VisualLintButtonSize" />

    <!-- Label above event list -->
    <TextView
        android:id="@+id/eventsLabel"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="@string/your_events"
        android:textSize="18sp"
        android:textStyle="bold"
        android:layout_marginBottom="8dp" />

    <!-- ListView to display events -->
    <ListView
        android:id="@+id/eventListView"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:divider="@android:color/darker_gray"
        android:dividerHeight="1dp"
        android:contentDescription="@string/event_list_description" />

</LinearLayout>

# Item_event.xml


<?xml version="1.0" encoding="utf-8"?>
<!-- Root LinearLayout for a single event item (horizontal row) -->
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:orientation="horizontal"
    android:padding="12dp"
    android:gravity="center_vertical"
    android:background="@android:color/white">

    <!-- Left side: Container holding event name and date stacked vertically -->
    <LinearLayout
        android:layout_width="0dp"
        android:layout_weight="1"
        android:layout_height="wrap_content"
        android:orientation="vertical">

        <!-- Event name display -->
        <TextView
            android:id="@+id/eventName"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="Event Name"
            android:textStyle="bold"
            android:textSize="16sp"
            android:paddingBottom="4dp"
            tools:ignore="HardcodedText" />
        <!-- Event date display -->
        <TextView
            android:id="@+id/eventDate"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="Date: 01/01/2025"
            android:textSize="14sp"
            tools:ignore="HardcodedText" />
    </LinearLayout>

    <!-- Right side: Button to delete this event entry -->
    <Button
        android:id="@+id/deleteButton"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Delete"
        tools:ignore="HardcodedText,VisualLintButtonSize" />
</LinearLayout>


# String.xml

<resources>
    <!-- App name displayed on the launcher -->
    <string name="app_name">EventPlanningAPP_BrennanHaggett</string>

    <!-- Greeting screen strings -->
    <string name="hint_enter_name">Enter your name</string>
    <string name="button_say_hello">Say Hello</string>
    <string name="greeting_format">Hello, %1$s!</string>

    <!-- Login screen strings -->
    <string name="login_button_text">Login</string>
    <string name="create_account_button_text">Create Account</string>
    <string name="username_hint">Enter username</string>
    <string name="password_hint">Enter password</string>

    <!-- SMS Notification screen strings -->
    <string name="sms_header">SMS Notifications</string>
    <string name="permission_status_denied">Permission not granted</string>
    <string name="request_sms_permission">Request SMS Permission</string>

    <!-- Event list screen strings (NEW) -->
    <string name="add_event">Add New Event</string>
    <string name="your_events">Your Events</string>
    <string name="event_list_description">List of all saved events</string>
</resources>

