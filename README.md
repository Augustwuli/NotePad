# NotePad——android备忘录

### 一、基本功能实现

1、主界面实现

![主界面](https://github.com/Augustwuli/NotePad/blob/master/image/1.png)

（1）当点击下方的添加按钮会进入编辑界面

实现代码：

```
fab.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View view) {
                Intent intent=new Intent(MainActivity.this,CreateNewNoteActivity.class);
                startActivity(intent);
            }
        });
```

（2）当点击右上角的放大镜会对所有便签进行搜索

实现代码：

```
 public void showNotes(String str){
        Cursor cursor=db.rawQuery("select * from notes where title like '%"+str+"%'",null);
        if(cursor.moveToFirst()){
            do {
                int nid=cursor.getInt(0);
                String ntitle=cursor.getString(1);
                String nbody=cursor.getString(2);
                String date=cursor.getString(3);
                Note note=new Note(nid,ntitle,nbody,date);
                noteList.add(note);
            }while (cursor.moveToNext());
        }
    }
```

2、便签编辑界面

![编辑界面](https://github.com/Augustwuli/NotePad/blob/master/image/2.png)

（1）当点击保存时，此数据便会插入数据库当中，并且获取当前时间

![](https://github.com/Augustwuli/NotePad/blob/master/image/3.png)

实现代码：

```
public void bindListening(){
        saveButton.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                String title=titleEdit.getText().toString();
                String body=bodyEdit.getText().toString();
                SimpleDateFormat simpleDateFormat=new SimpleDateFormat("yyyy.MM.dd HH:m");
                Date date=new Date(System.currentTimeMillis());
                String cdate=simpleDateFormat.format(date);
                db.execSQL("insert into notes(title,body,date) values('"+title+"','"+body+"','"+cdate+"')");
                Toast.makeText(CreateNewNoteActivity.this,"添加成功!",Toast.LENGTH_SHORT).show();
                MainActivity.myHandler.sendEmptyMessage(0x123);
                finish();
            }
        });
```

（2）当点击删除时，此数据便会从数据库中移出

![](https://github.com/Augustwuli/NotePad/blob/master/image/8.png)

```
public boolean onOptionsItemSelected(MenuItem item) {
        int id = item.getItemId();
        if(id==R.id.ed_delete){
            db=dbHelper.getWritableDatabase();
            db.execSQL("delete from notes where id="+this.id);
            Message message=new Message();
            message.arg1=position;
            message.what=0x125;
            MainActivity.myHandler.sendMessage(message);
            finish();
        }

        return super.onOptionsItemSelected(item);
    }
```

（3）当重新打开便签，并对内容进行修改保存，则会重新获取时间提示修改成功。

![](https://github.com/Augustwuli/NotePad/blob/master/image/3.png)

```
 public void bindListening(){
        saveButton.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                db=dbHelper.getWritableDatabase();
                String title=titleEdit.getText().toString();
                String body=bodyEdit.getText().toString();
                SimpleDateFormat simpleDateFormat=new SimpleDateFormat("yyyy.MM.dd. HH:mm");
                Date date=new Date(System.currentTimeMillis());
                String cdate=simpleDateFormat.format(date);
                db.execSQL("update notes set title=?,body=?,date=? where id="+id,new String[]{title,body,cdate});
                Toast.makeText(EditNoteActivity.this,"修改成功!",Toast.LENGTH_SHORT).show();
                Note note=new Note(id,title,body,cdate);
                Message message=new Message();
                message.what=0x124;
                message.obj=note;
                message.arg1=position;
                MainActivity.myHandler.sendMessage(message);
                finish();
            }
        });
```

3、便签搜索界面

![](https://github.com/Augustwuli/NotePad/blob/master/image/6.png)

![](https://github.com/Augustwuli/NotePad/blob/master/image/7.png)

实现代码：

```
public void showNotes(String str){
        Cursor cursor=db.rawQuery("select * from notes where title like '%"+str+"%'",null);
        if(cursor.moveToFirst()){
            do {
                int nid=cursor.getInt(0);
                String ntitle=cursor.getString(1);
                String nbody=cursor.getString(2);
                String date=cursor.getString(3);
                Note note=new Note(nid,ntitle,nbody,date);
                noteList.add(note);
            }while (cursor.moveToNext());
        }
    }
```

### 二、扩展功能实现

UI美化

![](https://github.com/Augustwuli/NotePad/blob/master/image/1.png)

![](https://github.com/Augustwuli/NotePad/blob/master/image/2.png)

![](https://github.com/Augustwuli/NotePad/blob/master/image/3.png)