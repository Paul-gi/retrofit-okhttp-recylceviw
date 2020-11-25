# retrofit-okhttp-recylceviw

------------------------------------------------------------------------------------------------------------------------------------
MainActivity
------------------------------------------------------------------------------------------------------------------------------------
package com.sinopac.hardtest;

import android.content.Intent;
import android.os.Bundle;
import android.widget.Button;

import androidx.appcompat.app.AppCompatActivity;
import androidx.recyclerview.widget.DividerItemDecoration;
import androidx.recyclerview.widget.LinearLayoutManager;
import androidx.recyclerview.widget.RecyclerView;

import com.sinopac.hardtest.Adapter.MyListAdapter;
import com.sinopac.hardtest.Data.Post;

import java.util.List;

import retrofit2.Call;
import retrofit2.Callback;
import retrofit2.Response;

public class MainActivity extends AppCompatActivity {

    private MyListAdapter myListAdapter = new MyListAdapter(v -> {
        Post post = (Post) v.getTag();
        Intent intent = new Intent(this, itemdatapage.class);
        intent.putExtra("ID", post.getId());
        intent.putExtra("USERID", post.getUserId());
        intent.putExtra("TITLE", post.getTitle());
        intent.putExtra("BODY", post.getBody());
        startActivity(intent);
    });

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        initView();
        initData();
    }


    private void initView() {
        Button changepage = findViewById(R.id.change);
        RecyclerView mRecyclerView = findViewById(R.id.recycleviewone);
        mRecyclerView.setLayoutManager(new LinearLayoutManager(this));
        mRecyclerView.addItemDecoration(new DividerItemDecoration(this, DividerItemDecoration.VERTICAL));
        mRecyclerView.setAdapter(myListAdapter);


        changepage.setOnClickListener(v -> {
            Intent intent = new Intent();
            intent.setClass(MainActivity.this, NewViewPage.class);
            startActivity(intent);
        });

    }


    private void initData() {
        Retro_http retro = Retro_http.getInstance();
        retro.callPosts(new Callback<List<Post>>() {
            @Override
            public void onResponse(Call<List<Post>> call, Response<List<Post>> response) {

                List<Post> result = response.body();
                runOnUiThread(() -> myListAdapter.setData(result));
//                mainActivity.runOnUiThread(() -> {
//                    myListAdapter.setData(result);
//                    myListAdapter.notifyDataSetChanged();
//                });
// -------------------------------------------------------------------------------------
//                Log.d("onResponse test", "result:" + result.toString());
//                // 透過 foreach 取出每一筆資料內容
//                for (Post item : response.body()
//                ) {
//                 Log.i("HKT", "id: " + item.getId());
//                    Log.d("HKT", "title: " + item.getTitle());
////                     sss = item.getTitle().toString();
//////                    Log.d("HKT", "body: " + item.getBody());
//////                    Log.d("HKT", "userId: " + item.getUserId());
                //   }
            }

            @Override
            public void onFailure(Call call, Throwable t) {

            }
        });
    }

}
------------------------------------------------------------------------------------------------------------------------------------------------------------
NewViewPage
-------------------------------------------------------------------------------------------------------------------------------------------------------------
package com.sinopac.hardtest;

import android.app.Activity;
import android.os.Bundle;

import androidx.annotation.Nullable;
import androidx.appcompat.app.AppCompatActivity;
import androidx.recyclerview.widget.DividerItemDecoration;
import androidx.recyclerview.widget.LinearLayoutManager;
import androidx.recyclerview.widget.RecyclerView;

import com.sinopac.hardtest.Adapter.MyListAdapterThree;
import com.sinopac.hardtest.Adapter.MyListAdapterTwo;
import com.sinopac.hardtest.Data.Comments;
import com.sinopac.hardtest.Data.Post;

import java.util.List;

import retrofit2.Call;
import retrofit2.Callback;
import retrofit2.Response;

public class NewViewPage extends AppCompatActivity {

    private final MyListAdapterTwo myListAdaptertwo = new MyListAdapterTwo();
    private final MyListAdapterThree myListAdapterthree = new MyListAdapterThree();
    Activity activity;

    @Override
    protected void onCreate(@Nullable Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.newreclycerviewpage);
        initView();
        initData();
        initDataTwo();
    }

    private void initView() {
        RecyclerView mRecyclerViewtwo = findViewById(R.id.recycleviewtwo);
        RecyclerView mRecyclerViewthree = findViewById(R.id.recycleviewthree);

        mRecyclerViewtwo.setLayoutManager(new LinearLayoutManager(this));
        mRecyclerViewtwo.addItemDecoration(new DividerItemDecoration(this, DividerItemDecoration.VERTICAL));
        mRecyclerViewtwo.setAdapter(myListAdaptertwo);

        mRecyclerViewthree.setLayoutManager(new LinearLayoutManager(this));
        mRecyclerViewthree.addItemDecoration(new DividerItemDecoration(this, DividerItemDecoration.VERTICAL));
        mRecyclerViewthree.setAdapter(myListAdapterthree);


    }

    private void initData() {
        Retro_http retro = Retro_http.getInstance();
        retro.callComments( new Callback<List<Comments>>() {
            @Override
            public void onResponse(Call<List<Comments>> call, Response<List<Comments>> response) {
                List<Comments> result = response.body();
                runOnUiThread(() -> myListAdaptertwo.setData(result));
            }

            @Override
            public void onFailure(Call call, Throwable t) {

            }
        });
    }

    private void initDataTwo() {
        Retro_http retro = Retro_http.getInstance();
        retro.callPosts( new Callback<List<Post>>() {
            @Override
            public void onResponse(Call<List<Post>> call, Response<List<Post>> response) {
                List<Post> result = response.body();
                runOnUiThread(() -> myListAdapterthree.setData(result,activity));
            }

            @Override
            public void onFailure(Call call, Throwable t) {

            }
        });
    }
}
------------------------------------------------------------------------------------------------------------------------------------------------------
itemdatapage
------------------------------------------------------------------------------------------------------------------------------------------------------
package com.sinopac.hardtest;

import android.os.Bundle;
import android.widget.TextView;

import androidx.annotation.Nullable;
import androidx.appcompat.app.AppCompatActivity;

public class itemdatapage extends AppCompatActivity {


    @Override
    protected void onCreate(@Nullable Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.itemdatapage);
        init();
    }


    private void init() {

        TextView txid = findViewById(R.id.txid);
        TextView txuserid = findViewById(R.id.txuserid);
        TextView txbody = findViewById(R.id.txbody);
        TextView txtitle = findViewById(R.id.txtitle);

        Bundle bundle = this.getIntent().getExtras();
        String id = bundle.getString("ID");
        txid.setText(id);
        String body = bundle.getString("BODY");
        txbody.setText(body);
        String title = bundle.getString("TITLE");
        txtitle.setText(title);
        String userid = bundle.getString("USERID");
        txuserid.setText(userid);
    }
}
-------------------------------------------------------------------------------------------------------------------------------------------------------
Retrofit+OKhttp
-------------------------------------------------------------------------------------------------------------------------------------------------------
package com.sinopac.hardtest;

import com.sinopac.hardtest.Data.Comments;
import com.sinopac.hardtest.Data.Post;

import java.util.List;
import java.util.concurrent.Executors;
import java.util.concurrent.TimeUnit;

import okhttp3.OkHttpClient;
import retrofit2.Call;
import retrofit2.Callback;
import retrofit2.Retrofit;
import retrofit2.converter.gson.GsonConverterFactory;

public class Retro_http {
    public static Retro_http instance;

    public synchronized static Retro_http getInstance() {
        if (instance == null) {
            instance = new Retro_http();
        }
        return instance;
    }

    private final Retrofit retrofit = new Retrofit.Builder()
            .baseUrl("https://jsonplaceholder.typicode.com")
            .addConverterFactory(GsonConverterFactory.create())
            .callbackExecutor(Executors.newSingleThreadExecutor())
            .client(okHttpClient())
            .build();

    private Retro_http() {
    }

    public void callPosts(Callback<List<Post>> callback) {
        FakeAPIService fakeAPIService = retrofit.create(FakeAPIService.class);
        //執行連線服務，透過 Callback 來等待回傳成功或失敗的資料
        Call<List<Post>> call = fakeAPIService.getPosts();
        call.enqueue(callback);
    }

    public void callComments(Callback<List<Comments>> callback) {
        FakeAPIService fakeAPIService = retrofit.create(FakeAPIService.class);
        Call<List<Comments>> call = fakeAPIService.getComments();
        call.enqueue(callback);
    }

    private OkHttpClient okHttpClient() {
        OkHttpClient result = null;
        try {
            result = new OkHttpClient.Builder()
                    .addNetworkInterceptor(chain -> chain.proceed(chain.request()))
                    .retryOnConnectionFailure(true)
                    .readTimeout(100, TimeUnit.SECONDS)
                    .connectTimeout(100, TimeUnit.SECONDS)
                    .build();
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            return result;
        }
    }


}
-------------------------------------------------------------------------------------------------------------------------------------------------
FakeAPIService  (interface)
-------------------------------------------------------------------------------------------------------------------------------------------------
package com.sinopac.hardtest;

import com.sinopac.hardtest.Data.Comments;
import com.sinopac.hardtest.Data.Post;

import java.util.List;

import retrofit2.Call;
import retrofit2.http.GET;

public interface FakeAPIService {
    @GET("/posts/1")
        //annotation 註解宣告方式定義 HTTP 連線獲取資料方法與指定API後網址
    Call<Post> getPost();

    //取得多筆資料
    @GET("/posts")
    Call<List<Post>> getPosts();

    //取得多筆資料
    @GET("/comments")
    Call<List<Comments>> getComments();
}
---------------------------------------------------------------------------------------------------------------------------------------------------
MyAdapter (main)
-------------------------------------------------------------------------------------------------------------------------------------------------
package com.sinopac.hardtest.Adapter;

import android.content.Intent;
import android.os.Bundle;
import android.view.LayoutInflater;
import android.view.View;
import android.view.ViewGroup;
import android.widget.TextView;

import androidx.annotation.NonNull;
import androidx.recyclerview.widget.RecyclerView;

import com.sinopac.hardtest.Data.Post;
import com.sinopac.hardtest.R;

import java.util.ArrayList;
import java.util.List;


public class MyListAdapter extends RecyclerView.Adapter<MyListAdapter.ViewHolder> {
    private final ArrayList<Post> data = new ArrayList<>();

    private final View.OnClickListener onClickListener;

    //為了可以點及觸發事件新增的建構子
    public MyListAdapter(View.OnClickListener onClickListener) {
        this.onClickListener = onClickListener;
    }

    public void setData(List<Post> data) {
        this.data.clear();
        this.data.addAll(data);
        notifyDataSetChanged();
    }


    class ViewHolder extends RecyclerView.ViewHolder {
        private TextView tvId, tvSub1, tvSub2, tvAvg;
        private View mView;

        public ViewHolder(@NonNull View itemView) {
            super(itemView);
            tvId = itemView.findViewById(R.id.textView_Id);
            tvSub1 = itemView.findViewById(R.id.textView_sub1);
            tvSub2 = itemView.findViewById(R.id.textView_sub2);
            tvAvg = itemView.findViewById(R.id.textView_avg);
            mView = itemView;
        }
    }


    @NonNull
    @Override
    public ViewHolder onCreateViewHolder(@NonNull ViewGroup parent, int viewType) {
        View view = LayoutInflater.from(parent.getContext())
                .inflate(R.layout.recycle_item, parent, false);
        return new ViewHolder(view);
    }

    @Override
    public void onBindViewHolder(@NonNull ViewHolder holder, int position) {
        Post post = data.get(position);
        holder.tvId.setText(post.getId());
        holder.tvAvg.setText(post.getUserId());
        holder.tvSub1.setText(post.getTitle());
        holder.tvSub2.setText(post.getBody());
        holder.mView.setOnClickListener(onClickListener);
        holder.mView.setTag(post);
    }

    @Override
    public int getItemCount() {
        return data.size();
    }
}

--------------------------------------------------------------------------------------------------------------------------------------------------------------------
MyListAdapterTwo
--------------------------------------------------------------------------------------------------------------------------------------------------------------------
package com.sinopac.hardtest.Adapter;

import android.view.LayoutInflater;
import android.view.View;
import android.view.ViewGroup;
import android.widget.TextView;

import androidx.annotation.NonNull;
import androidx.recyclerview.widget.RecyclerView;

import com.sinopac.hardtest.Data.Comments;
import com.sinopac.hardtest.Data.Post;
import com.sinopac.hardtest.R;

import java.util.ArrayList;
import java.util.List;

public class MyListAdapterTwo extends RecyclerView.Adapter<MyListAdapterTwo.ViewHolder> {
    private final ArrayList<Comments> data = new ArrayList<>();

    public void setData(List<Comments> data) {
        this.data.clear();
        this.data.addAll(data);
        notifyDataSetChanged();
    }

    class ViewHolder extends RecyclerView.ViewHolder {
        private TextView tvId, tvSub1, tvSub2, tvAvg,tvEmail;

        public ViewHolder(@NonNull View itemView) {
            super(itemView);
            tvId = itemView.findViewById(R.id.textView_Id);
            tvSub1 = itemView.findViewById(R.id.textView_sub1);
            tvSub2 = itemView.findViewById(R.id.textView_sub2);
            tvAvg = itemView.findViewById(R.id.textView_avg);
            tvEmail = itemView.findViewById(R.id.textView_email);
        }
    }


    @NonNull
    @Override
    public MyListAdapterTwo.ViewHolder onCreateViewHolder(@NonNull ViewGroup parent, int viewType) {
        View view = LayoutInflater.from(parent.getContext())
                .inflate(R.layout.recycle_itemtwo, parent, false);
        return new MyListAdapterTwo.ViewHolder(view);
    }

    @Override
    public void onBindViewHolder(@NonNull MyListAdapterTwo.ViewHolder holder, int position) {
        Comments info = data.get(position);
        holder.tvId.setText(info.getId());
        holder.tvAvg.setText(info.getpostId());
        holder.tvSub1.setText(info.getname());
        holder.tvSub2.setText(info.getBody());
        holder.tvEmail.setText(info.getemail());
    }

    @Override
    public int getItemCount() {
        return data.size();
    }

}
----------------------------------------------------------------------------------------------------------------------------------------------------------------
MyListAdapterThree
----------------------------------------------------------------------------------------------------------------------------------------------------------------
package com.sinopac.hardtest.Adapter;

import android.app.Activity;
import android.app.AlertDialog;
import android.app.Dialog;
import android.view.LayoutInflater;
import android.view.View;
import android.view.ViewGroup;
import android.widget.EditText;
import android.widget.TextView;

import androidx.annotation.NonNull;
import androidx.recyclerview.widget.RecyclerView;

import com.sinopac.hardtest.Data.Post;
import com.sinopac.hardtest.R;

import java.util.ArrayList;
import java.util.List;

public class MyListAdapterThree extends RecyclerView.Adapter<MyListAdapterThree.ViewHolder> {
    private final ArrayList<Post> data = new ArrayList<>();
    Activity activity;
    public void setData(List<Post> data, Activity activity) {
        this.data.clear();
        this.data.addAll(data);
        this.activity=activity;
        notifyDataSetChanged();
    }

    class ViewHolder extends RecyclerView.ViewHolder {
        private TextView tvId, tvSub1, tvSub2, tvAvg;
        private View mView;

        public ViewHolder(@NonNull View itemView) {
            super(itemView);
            tvId = itemView.findViewById(R.id.textView_Id);
            tvSub1 = itemView.findViewById(R.id.textView_sub1);
            tvSub2 = itemView.findViewById(R.id.textView_sub2);
            tvAvg = itemView.findViewById(R.id.textView_avg);
            mView = itemView;
        }
    }


    @NonNull
    @Override
    public MyListAdapterThree.ViewHolder onCreateViewHolder(@NonNull ViewGroup parent, int viewType) {
        View view = LayoutInflater.from(parent.getContext())
                .inflate(R.layout.recycle_itemthree, parent, false);
        return new MyListAdapterThree.ViewHolder(view);
    }

    @Override
    public void onBindViewHolder(@NonNull MyListAdapterThree.ViewHolder holder, int position) {
        Post info = data.get(position);
        holder.tvId.setText(info.getId());
        holder.tvAvg.setText(info.getUserId());
        holder.tvSub1.setText(info.getTitle());
        holder.tvSub2.setText(info.getBody());

//        holder.mView.setOnClickListener(new View.OnClickListener(){
//            @Override
//            public void onClick(View v) {
//                AlertDialog.Builder builder = new AlertDialog.Builder(activity);
//                builder.setTitle(info.getId());
////                        .setMessage(info.getBody())
////                        .setCancelable(false);
////                //在這裡加入一個edittext
////                final EditText et = new EditText(activity);
////                builder.setView(et);
//            }
//        });

    }

    @Override
    public int getItemCount() {
        return data.size();
    }
}
-------------------------------------------------------------------------------------------------------------------------------------------------------------
Post
------------------------------------------------------------------------------------------------------------------------------------------------------------
package com.sinopac.hardtest.Data;

public class Post {
    private int id;
    private String title;
    private String body;
    private int userId;

    private String a;
    private String b;

    public String getId() {
        setId(id);
        return b;
    }

    public void setId(int id) {
        this.id = id;
        b = Integer.toString(id);
    }

    public String getTitle() {
        return title;
    }

    public void setTitle(String title) {
        this.title = title;
    }

    public String getBody() {
        return body;
    }

    public void setBody(String body) {
        this.body = body;
    }

    public String getUserId() {
        setUserId(userId);
        return a;
    }

    public void setUserId(int userId) {
        this.userId = userId;
        a = Integer.toString(userId);
    }
}
-----------------------------------------------------------------------------------------------------------------------------------------------------------------------
Comments
----------------------------------------------------------------------------------------------------------------------------------------------------------------------
package com.sinopac.hardtest.Data;

public class Comments {
    private int postId;
    private int id;
    private String name;
    private String body;
    private String email;

    private String a;
    private String b;

    public String getpostId() {
        setpostId(postId);
        return a;
    }

    public void setpostId(int postId) {
        this.postId = postId;
        a = Integer.toString(postId);
    }

    public String getId() {
        setId(id);
        return b;
    }

    public void setId(int id) {
        this.id = id;
        b = Integer.toString(id);
    }


    public String getname() {
        return name;
    }

    public void setname(String name) {
        this.name = name;
    }

    public String getBody() {
        return body;
    }

    public void setBody(String body) {
        this.body = body;
    }

    public String getemail() {
        return email;
    }

    public void setemail(String email) {
        this.email = email;
    }
}
------------------------------------------------------------------------------------------------------------------------------------------------------
XML
-----------------------------------------------------------------------------------------------------------------------------------------------------
activity_main
----------------------------------------------------------------------------------------------------------------------------------------------------
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    <androidx.recyclerview.widget.RecyclerView
        android:id="@+id/recycleviewone"
        android:layout_width="334dp"
        android:layout_height="565dp"
        android:layout_marginTop="8dp"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintHorizontal_bias="0.493"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toBottomOf="@+id/change" />

    <Button
        android:id="@+id/change"
        android:layout_width="394dp"
        android:layout_height="43dp"
        android:layout_marginTop="64dp"
        android:text="ChangePage"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintHorizontal_bias="0.47"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent" />
</androidx.constraintlayout.widget.ConstraintLayout>
------------------------------------------------------------------------------------------------------------------------------------------------------------
itemdatapage
-------------------------------------------------------------------------------------------------------------------------------------------------------------
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <TextView
        android:id="@+id/txbody"
        android:layout_width="304dp"
        android:layout_height="199dp"
        android:layout_marginStart="60dp"
        android:text="TextView"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toBottomOf="@+id/txid" />

    <TextView
        android:id="@+id/txid"
        android:layout_width="304dp"
        android:layout_height="47dp"
        android:layout_marginStart="60dp"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toBottomOf="@+id/textView9" />

    <TextView
        android:id="@+id/txuserid"
        android:layout_width="305dp"
        android:layout_height="86dp"
        android:layout_marginStart="60dp"
        android:text="TextView"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toBottomOf="@+id/txtitle" />

    <TextView
        android:id="@+id/txtitle"
        android:layout_width="304dp"
        android:layout_height="75dp"
        android:layout_marginStart="60dp"
        android:text="TextView"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toBottomOf="@+id/txbody" />

    <TextView
        android:id="@+id/textView9"
        android:layout_width="202dp"
        android:layout_height="58dp"
        android:layout_marginTop="40dp"
        android:text="詳細資料"
        android:textSize="48dp"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent" />

</androidx.constraintlayout.widget.ConstraintLayout>
-------------------------------------------------------------------------------------------------------------------------------------------------------
newcycleviewpage
-----------------------------------------------------------------------------------------------------------------------------------------
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:background="#808080">

    <androidx.recyclerview.widget.RecyclerView
        android:id="@+id/recycleviewtwo"
        android:layout_width="359dp"
        android:layout_height="313dp"
        android:layout_marginTop="16dp"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent"
        tools:ignore="MissingConstraints" />

    <androidx.recyclerview.widget.RecyclerView
        android:id="@+id/recycleviewthree"
        android:layout_width="359dp"
        android:layout_height="331dp"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toBottomOf="@+id/textView3"
        tools:ignore="MissingConstraints" />

    <TextView
        android:id="@+id/textView3"
        android:layout_width="412dp"
        android:layout_height="22dp"
        android:background="#808000"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toBottomOf="@+id/recycleviewtwo" />

</androidx.constraintlayout.widget.ConstraintLayout>
------------------------------------------------------------------------------------------------------------------------------------------------------------
recycleview_item  (one)
-------------------------------------------------------------------------------------------------------------------------------------------------------
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:layout_margin="10dp"
    android:background="#ECEBEB"
    android:orientation="vertical">


    <TextView
        android:id="@+id/textView_Id"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:paddingStart="10dp"
        android:paddingTop="20dp"
        android:paddingBottom="20dp"
        android:text="●●●："
        android:textAppearance="@style/TextAppearance.AppCompat.Large"
        android:textColor="?attr/colorBackgroundFloating" />

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:layout_marginStart="20dp"
        android:layout_marginTop="10dp"
        android:orientation="horizontal">

        <TextView
            android:id="@+id/textView2"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_weight="0.3"
            android:text="●："
            android:textAppearance="@style/TextAppearance.AppCompat.Large"
            android:textColor="#000"
            android:textSize="18sp" />

        <TextView
            android:id="@+id/textView_sub1"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_weight="8"
            android:text="TextView"
            android:textAppearance="@style/TextAppearance.AppCompat.Large"
            android:textColor="#000"
            android:textSize="18sp" />
    </LinearLayout>

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:layout_marginStart="20dp"
        android:layout_marginTop="10dp"
        android:orientation="horizontal">

        <TextView
            android:id="@+id/textView4"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_weight="0.3"
            android:text="●："
            android:textAppearance="@style/TextAppearance.AppCompat.Large"
            android:textColor="#000"
            android:textSize="18sp" />

        <TextView
            android:id="@+id/textView_sub2"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_weight="8"
            android:text="TextView"
            android:textAppearance="@style/TextAppearance.AppCompat.Large"
            android:textColor="#000"
            android:textSize="18sp" />
    </LinearLayout>

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:layout_marginStart="20dp"
        android:layout_marginTop="10dp"
        android:layout_marginBottom="10dp"
        android:orientation="horizontal">

        <TextView
            android:id="@+id/textView6"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_weight="0.3"
            android:text="●："
            android:textAppearance="@style/TextAppearance.AppCompat.Large"
            android:textColor="#000"
            android:textSize="18sp" />

        <TextView
            android:id="@+id/textView_avg"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_weight="8"
            android:text="TextView"
            android:textAppearance="@style/TextAppearance.AppCompat.Large"
            android:textColor="#000"
            android:textSize="18sp" />

    </LinearLayout>
</LinearLayout>
--------------------------------------------------------------------------------------------------------------------------------------------------------
recycleviewTwo
----------------------------------------------------------------------------------------------------------------------------------------------------
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:layout_margin="10dp"
    android:background="#ECEBEB"
    android:orientation="vertical">




    <TextView
        android:id="@+id/textView_Id"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:paddingStart="10dp"
        android:paddingTop="20dp"
        android:paddingBottom="20dp"
        android:text="~~~~~!!!~~~~："
        android:textAppearance="@style/TextAppearance.AppCompat.Large"
        android:textColor="?attr/colorBackgroundFloating" />

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:layout_marginStart="20dp"
        android:layout_marginTop="10dp"
        android:orientation="horizontal">

        <TextView
            android:id="@+id/textView2"
            android:layout_width="60dp"
            android:layout_height="match_parent"
            android:layout_weight="0.3"
            android:text="A："
            android:textAppearance="@style/TextAppearance.AppCompat.Large"
            android:textColor="#000"
            android:textSize="18sp" />

        <TextView
            android:id="@+id/textView_sub1"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_weight="8"
            android:text="TextView"
            android:textAppearance="@style/TextAppearance.AppCompat.Large"
            android:textColor="#000"
            android:textSize="18sp" />
    </LinearLayout>

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:layout_marginStart="20dp"
        android:layout_marginTop="10dp"
        android:orientation="horizontal">

        <TextView
            android:id="@+id/textView4"
            android:layout_width="60dp"
            android:layout_height="match_parent"
            android:layout_weight="0.3"
            android:text="B："
            android:textAppearance="@style/TextAppearance.AppCompat.Large"
            android:textColor="#000"
            android:textSize="18sp" />

        <TextView
            android:id="@+id/textView_sub2"
            android:layout_width="249dp"
            android:layout_height="match_parent"
            android:layout_weight="8"
            android:text="TextView"
            android:textAppearance="@style/TextAppearance.AppCompat.Large"
            android:textColor="#000"
            android:textSize="18sp" />
    </LinearLayout>

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:layout_marginStart="20dp"
        android:layout_marginTop="10dp"
        android:orientation="horizontal">

        <TextView
            android:id="@+id/textView6"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_weight="0.3"
            android:text="post："
            android:textAppearance="@style/TextAppearance.AppCompat.Large"
            android:textColor="#000"
            android:textSize="18sp" />

        <TextView
            android:id="@+id/textView_avg"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_weight="8"
            android:text="TextView"
            android:textAppearance="@style/TextAppearance.AppCompat.Large"
            android:textColor="#000"
            android:textSize="18sp" />

    </LinearLayout>

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:layout_marginStart="20dp"
        android:layout_marginTop="10dp"
        android:layout_marginBottom="10dp"
        android:orientation="horizontal">

        <TextView
            android:id="@+id/textView7"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_weight="0.3"
            android:text="mail："
            android:textAppearance="@style/TextAppearance.AppCompat.Large"
            android:textColor="#000"
            android:textSize="18sp" />

        <TextView
            android:id="@+id/textView_email"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_weight="8"
            android:text="TextView"
            android:textAppearance="@style/TextAppearance.AppCompat.Large"
            android:textColor="#000"
            android:textSize="18sp" />
    </LinearLayout>
</LinearLayout>
---------------------------------------------------------------------------------------------------------------------------------------------------------
recycleViewThree
----------------------------------------------------------------------------------------------------------------------------------------------------------
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:layout_margin="10dp"
    android:background="#ECEBEB"
    android:orientation="vertical">


    <TextView
        android:id="@+id/textView_Id"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:paddingStart="10dp"
        android:paddingTop="20dp"
        android:paddingBottom="20dp"
        android:text="GGGGGG："
        android:textAppearance="@style/TextAppearance.AppCompat.Large"
        android:textColor="?attr/colorBackgroundFloating" />

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:layout_marginStart="20dp"
        android:layout_marginTop="10dp"
        android:orientation="horizontal">

        <TextView
            android:id="@+id/textView2"
            android:layout_width="60dp"
            android:layout_height="match_parent"
            android:layout_weight="0.3"
            android:text="A："
            android:textAppearance="@style/TextAppearance.AppCompat.Large"
            android:textColor="#000"
            android:textSize="18sp" />

        <TextView
            android:id="@+id/textView_sub1"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_weight="8"
            android:text="TextView"
            android:textAppearance="@style/TextAppearance.AppCompat.Large"
            android:textColor="#000"
            android:textSize="18sp" />
    </LinearLayout>

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:layout_marginStart="20dp"
        android:layout_marginTop="10dp"
        android:orientation="horizontal">

        <TextView
            android:id="@+id/textView4"
            android:layout_height="wrap_content"
            android:layout_weight="0.3"
            android:layout_width="60dp"
            android:text="B："
            android:textAppearance="@style/TextAppearance.AppCompat.Large"
            android:textColor="#000"
            android:textSize="18sp" />

        <TextView
            android:id="@+id/textView_sub2"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_weight="8"
            android:text="TextView"
            android:textAppearance="@style/TextAppearance.AppCompat.Large"
            android:textColor="#000"
            android:textSize="18sp" />
    </LinearLayout>

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:layout_marginStart="20dp"
        android:layout_marginTop="10dp"
        android:layout_marginBottom="10dp"
        android:orientation="horizontal">

        <TextView
            android:id="@+id/textView6"
            android:layout_width="wrap_content"
            android:layout_height="match_parent"
            android:layout_weight="0.3"
            android:text="Post："
            android:textAppearance="@style/TextAppearance.AppCompat.Large"
            android:textColor="#000"
            android:textSize="18sp" />

        <TextView
            android:id="@+id/textView_avg"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_weight="8"
            android:text="TextView"
            android:textAppearance="@style/TextAppearance.AppCompat.Large"
            android:textColor="#000"
            android:textSize="18sp" />
    </LinearLayout>
</LinearLayout>
---------------------------------------------------------------------------------------------------------------------------------------------------
