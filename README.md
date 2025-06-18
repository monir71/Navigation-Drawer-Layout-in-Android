Note here in actiivity_main.xml
We used DrawerLayout with android:fitsSystemWindows="true" and tools:openDrawer="start"
And a NavigationView with app:headerLayout="@layout/header_layout" and app:menu="@menu/nav_menu_items"

    <include layout="@layout/app_bar_main"
        android:layout_height="match_parent"
        android:layout_width="match_parent"/>

    <com.google.android.material.navigation.NavigationView
        android:layout_width="wrap_content"
        android:layout_height="match_parent"
        android:layout_gravity="start"
        android:fitsSystemWindows="true"
        android:id="@+id/navigationView"
        app:headerLayout="@layout/header_layout"
        app:menu="@menu/nav_menu_items"/>

</androidx.drawerlayout.widget.DrawerLayout>

Here in app_bar_main.xml we used Toolbar within AppBarLayout

<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:fitsSystemWindows="true"
    android:orientation="vertical">

    <com.google.android.material.appbar.AppBarLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content">

        <androidx.appcompat.widget.Toolbar
            android:id="@+id/toolbar"
            android:layout_width="match_parent"
            android:layout_height="?attr/actionBarSize"
            android:background="#9C27B0"
            app:menu="@menu/toolbar_menu"
            app:title="My ToolBar"/>

    </com.google.android.material.appbar.AppBarLayout>

    <include layout="@layout/content_main"/>
    
</LinearLayout>

Here in content_main.xml it is container of FrameLayout

<?xml version="1.0" encoding="utf-8"?>
<LinearLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical">

    <FrameLayout
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:background="@color/black"
        android:id="@+id/container"/>

</LinearLayout>

Here in header_layout.xml we used ImageView and TextView

<?xml version="1.0" encoding="utf-8"?>
<LinearLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:background="#9C27B0"
    android:orientation="vertical"
    android:layout_gravity="bottom"
    android:padding="30sp">

    <ImageView
        android:layout_width="100sp"
        android:layout_height="100sp"
        android:contentDescription="LogoImage"
        android:src="@drawable/svg_rose"/>

    <TextView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:textStyle="bold"
        android:textColor="@color/white"
        android:text="My Title"
        android:textSize="34sp"/>

    <TextView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="This is about my app."
        android:textColor="@color/white"
        android:textSize="17sp"/>

</LinearLayout>

Main Activity followed by. Note here override method onCreateOptionsMenu(Menu menu)

public class MainActivity extends AppCompatActivity {

    NavigationView navigationView;
    Toolbar toolbar;
    FrameLayout container;
    DrawerLayout drawerLayout;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        EdgeToEdge.enable(this);
        setContentView(R.layout.activity_main);

        navigationView = findViewById(R.id.navigationView);
        toolbar = findViewById(R.id.toolbar);
        container = findViewById(R.id.container);
        drawerLayout = findViewById(R.id.main);

        //Step 1
        setSupportActionBar(toolbar);

        ActionBarDrawerToggle toggle = new ActionBarDrawerToggle(this, drawerLayout, toolbar, R.string.open_drawer, R.string.close_drawer);
        drawerLayout.setDrawerListener(toggle);

        toggle.syncState();

        load_fragment(new AFragment(), 0);

        navigationView.setNavigationItemSelectedListener(new NavigationView.OnNavigationItemSelectedListener() {
            @Override
            public boolean onNavigationItemSelected(@NonNull MenuItem item) {
                int id = item.getItemId();

                if(id == R.id.nav_a)
                    load_fragment(new AFragment(), 1);
                else if(id == R.id.nav_b)
                    load_fragment(new BFragment(), 1);
                else if(id == R.id.nav_c)
                    load_fragment(new CFragment(), 1);
                else
                    Toast.makeText(MainActivity.this, "Clicked on Menu", Toast.LENGTH_SHORT).show();

                drawerLayout.closeDrawer(GravityCompat.START);

                return true;
            }
        });

        ViewCompat.setOnApplyWindowInsetsListener(findViewById(R.id.main), (v, insets) -> {
            Insets systemBars = insets.getInsets(WindowInsetsCompat.Type.systemBars());
            v.setPadding(systemBars.left, systemBars.top, systemBars.right, systemBars.bottom);
            return insets;
        });

    }

    @Override
    public void onBackPressed() {

        if(drawerLayout.isDrawerOpen(GravityCompat.START))
            drawerLayout.closeDrawer(GravityCompat.START);
        else
            super.onBackPressed();
    }

    public void load_fragment(Fragment fragment, int flag)
    {
        FragmentManager fm = getSupportFragmentManager();
        FragmentTransaction ft = fm.beginTransaction();
        if(flag == 0)
            ft.add(R.id.container, fragment);
        else
            ft.replace(R.id.container, fragment);

        ft.commit();
    }

    @Override
    public boolean onCreateOptionsMenu(Menu menu) {
        getMenuInflater().inflate(R.menu.toolbar_menu, menu);
        return true;
    }
}



