<!DOCTYPE html>



<html lang="ko">

<head>

<meta charset="UTF-8">

<meta name="viewport" content="width=device-width, initial-scale=1.0">



<script type="module">

import { createClient }

from 'https://esm.sh/@supabase/supabase-js'



const supabase = createClient(

    'https://szddmhwystebpmqqbxzh.supabase.co/rest/v1/',

    'sb_publishable_PqxjJWezuAvwEaBFqeF7eg_7QCGICJ3'

)



const ownerName = "마생";



async function loadMessages(){



    const { data } = await supabase

    .from('guestbook')

    .select('*')

    .order('created_at',{ascending:false})



    const chat =

    document.getElementById("chat");



    chat.innerHTML = "";



    data.forEach(msg=>{



        const badge =

        msg.nickname === ownerName

        ? `<span class="owner">주인장</span>`

        : '';



        chat.innerHTML += `



        <div class="msg">



            <div class="nick">

                🎀 ${msg.nickname}

                ${badge}

            </div>



            <div class="text">

                ${msg.message}

            </div>



            <div class="time">

                ${new Date(msg.created_at)

                .toLocaleString('ko-KR')}

            </div>



        </div>



        `;

    });



}



window.addEventListener("DOMContentLoaded",()=>{



    const saved =

    localStorage.getItem("guestbook_nickname");



    if(saved){

        document

        .getElementById("nickname")

        .value = saved;

    }



    loadMessages();



});



window.sendMessage = async()=>{



    const nickname =

    document

    .getElementById("nickname")

    .value

    .trim();



    const message =

    document

    .getElementById("message")

    .value

    .trim();



    if(!nickname || !message){

        alert("닉네임과 메시지를 입력해 주세요 ♡");

        return;

    }



    localStorage.setItem(

        "guestbook_nickname",

        nickname

    );



    await supabase

    .from('guestbook')

    .insert([

        {

            nickname,

            message

        }

    ]);



    document

    .getElementById("message")

    .value = '';



    loadMessages();

}



supabase

.channel('guestbook')

.on(

'postgres_changes',

{

event:'INSERT',

schema:'public',

table:'guestbook'

},

()=>{

loadMessages();

}

)

.subscribe();



</script>



<style>



@font-face{

    font-family:"Gmarket";

    src:url("https://cdn.jsdelivr.net/gh/projectnoonnu/noonfonts_2001@1.1/GmarketSansMedium.woff")

    format("woff");

}



:root{



    --pink:#ff8fc4;

    --light:#fff9fc;

    --border:#ffd5e8;



}



*{

    margin:0;

    padding:0;

    box-sizing:border-box;

}



body{



    font-family:"Gmarket";



    background:

    repeating-linear-gradient(

        45deg,

        #fff8fc,

        #fff8fc 12px,

        #fff3f8 12px,

        #fff3f8 24px

    );



    padding:10px;

}



.wrap{



    width:100%;

    max-width:400px;



    margin:auto;



    background:white;



    border:2px solid var(--border);



    border-radius:24px;



    overflow:hidden;



    box-shadow:

    0 0 15px rgba(255,182,217,.15);

}



.header{



    text-align:center;



    padding:15px;



    color:var(--pink);



    font-size:20px;



    border-bottom:2px solid var(--border);

}



.form{



    padding:15px;

}



input,

textarea{



    width:100%;



    padding:10px;



    border-radius:14px;



    border:2px solid var(--border);



    margin-bottom:8px;



    font-family:"Gmarket";



    outline:none;

}



textarea{



    height:80px;



    resize:none;

}



button{



    width:100%;



    padding:12px;



    border:none;



    border-radius:999px;



    background:

    linear-gradient(

        180deg,

        #ffd4e8,

        #ffb9d9

    );



    color:white;



    font-family:"Gmarket";



    cursor:pointer;

}



.chat{



    padding:12px;



    max-height:400px;



    overflow-y:auto;

}



.msg{



    background:#fffafd;



    border:2px solid var(--border);



    border-radius:18px;



    padding:12px;



    margin-bottom:10px;

}



.nick{



    color:var(--pink);



    font-size:13px;



    margin-bottom:5px;

}



.owner{



    margin-left:5px;



    background:#ffd8eb;



    color:#ff6db1;



    padding:2px 8px;



    border-radius:999px;



    font-size:11px;

}



.text{



    line-height:1.5;



    color:#444;

}



.time{



    color:#aaa;



    font-size:11px;



    margin-top:6px;

}



</style>



</head>



<body>



<div class="wrap">



```

<div class="header">

    ♡ GUEST BOOK ♡

</div>



<div class="form">



    <input

        id="nickname"

        placeholder="닉네임"

    >



    <textarea

        id="message"

        placeholder="메시지를 남겨주세요 ♡"

    ></textarea>



    <button onclick="sendMessage()">

        ♡ SEND ♡

    </button>



</div>



<div class="chat" id="chat"></div>

```



</div>



</body>

</html>
