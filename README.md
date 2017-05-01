 

 - 프로젝트 명 : [FireBaseOne]

 - 내용 : 파이어베이스 사용법을 익힙니다.

#1. FireBaseOne

--> 파이어베이스에서 제공하는 "Realtime Database"를 사용할 겁니다.

다음과 같은 순서로 진행하겠습니다.
							
							1. Firebase 프로젝트 생성
							2. Realtime Database 
							3. 안드로이드 프로젝트에 firebase 연결하기
							4. 데이터 저장 / 불러오기

##1.1 Firebase 프로젝트 생성

![](http://i.imgur.com/Esuz40W.png)

--> FireBaseOne 프로젝트를 생성합니다. 데이터가 저장될 저장소 역할을 합니다.

##1.2 Realtime Database

![](http://i.imgur.com/E0V5gwM.png)

--> 위 부분이 바로 데이터를 눈으로 확인할 수 있는 곳입니다. <키, 값> 조합으로 된 Map 데이터 구조입니다.

![](http://i.imgur.com/dT8hoVA.png)

--> "Realtime Database"에 있는 데이터 접근 권한을 설정합니다. 여기서는 'true'로 바꿔주어 프로젝트에 참여하는 모든 사람에게 권한을 허용하였습니다.

##1.3 안드로이드 프로젝트에 firebase 연결하기

[Tools] --> [FireBase] --> [Realtime Database -- Save and Retrieve data] --> [Connect your app to Firebase] --> 1.1에서 만든 프로젝트를 선택! --> [Add the Realtime DataBase to your app]

--> 위의 과정을 끝내고 나면, 파이어베이스를 사용하기 위한 모든 환경설정이 자동으로 처리됩니다.(라이브러리 추가 등등...)


##1.4 데이터 저장 / 불러오기

[MainActivity.java]

![](http://i.imgur.com/ubz4mdg.png)
![](http://i.imgur.com/gIEaQaD.png)
![](http://i.imgur.com/ZlgQUgz.png)


(1) 파이어베이스에 데이터를 읽고 쓰기 위해서는 DatabaseReference를 사용해야합니다. 먼저 파이어베이스와 연결합니다.

						FirebaseDatabase database = FirebaseDatabase.getInstance();

(2) CRUD 작업의 기준이 되는 노드를 레퍼런스로 가져옵니다.

						DatabaseReference bbsRef = database.getReference("bbs");

![](http://i.imgur.com/TDg6BLj.png)

(3) addValueEventListener : path에 저장되어있는 데이터를 읽어오거나 혹은, path에 존재하는데이터 변화가 감지되었을 때 사용하는 메소드입니다. 이 리스너를 통해서 콜백되는 함수가 onDataChange()입니다. ( 이 메소드에 담긴 snapshot으로 데이터를 읽어올 수 있습니다.)

						bbsRef.addValueEventListener(postListener); // 레퍼런스(bbsRef) 기준으로 데이터베이스에 쿼리를 날리는데, 자동으로 쿼리가 됩니다. 

(4) 리스트뷰에 목록을 세팅합니다.

      				  listView = (ListView)findViewById(R.id.listView);
				      adapter = new ListAdapter(datas, this);
        			  listView.setAdapter(adapter);

(5) <데이터 읽어오기>ValueEventListener : 경로의 전체 내용에 대한 변경을 읽고 수신 대기합니다. 

       			   datas.clear(); 		// 위에 선언한 저장소인 datas를 초기화합니다.(데이터가 바뀔때마다 같은 내용이 반복되서 datas에 저장되는 것을 방지합니다.)

onDataChange()에서 해줘야 할 일은 bbs 레퍼런스의 스냅샷을 가져와서 레퍼런스의 자식노드를 반복문을 통해 하나씩 꺼냅니다. 
getValue()는 현재 snapshot이 가리키고 있는 데이터를 자바 객체 형태로 반환해줍니다.( 이 자바 객체를 받아줄 Bbs 클래스를 생성해주어야 합니다.) 


 				   for( DataSnapshot snapshot : dataSnapshot.getChildren() ) {
     			       String key  = snapshot.getKey();
      		           Bbs bbs = snapshot.getValue(Bbs.class); // 컨버팅되서 Bbs 구조로 바꿉니다.
            		   bbs.key = key;
           			   datas.add(bbs);

        		   }

어뎁터에 리스트가 바뀌었다는 걸 알립니다.
 
       			   adapter.notifyDataSetChanged();


(6 - 1) <데이터 저장하기> bbs 레퍼런스 (테이블)에 키를 생성하고 그 키 값을 가져옵니다.
						
	                String key = bbsRef.push().getKey();

(6 - 2) 입력될 키, 값 세트 (레코드)를 생성합니다.

               		 Map<String, String > postValues = new HashMap<>(); // Map<key, value> 형태로 저장합니다.
            	     postValues.put("title", title);
         		     postValues.put("content", content);


(6 - 3) 생성된 레코드를 데이터베이스에 입력합니다.

                	DatabaseReference keyRef = bbsRef.child(key);
              	    keyRef.setValue(postValues);

![](http://i.imgur.com/3usyJ2q.png)