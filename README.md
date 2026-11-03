(function(){
  // ===== ここを書き換えて自分の内容にする =====
  // k:1=過去 2=現在 3=未来 / ev.type: spot(光る点) tap(写真をn回タップ) text(文章をn回タップ) hold(見出し長押し)
  var E=[
   {k:1,when:'3歳のころ',t:'いちばん昔の記憶',e:'🏠',g:'g1',ph:'ここに3歳のころの家の写真',tx:'いちばん昔の記憶は、3歳のときの家です。',
    ev:{type:'spot',x:'80%',y:'20%',em:'🌟',title:'かくれイベント',msg:'ここに秘密の思い出を書く。'}},
   {k:1,when:'小さいころ',t:'夢中になっていたこと',e:'🏎️',g:'g2',ph:'ここにマリオカートの写真',tx:'小さいころ夢中になっていたのは、マリオカートです。',
    hint:'写真をいろいろ触ってみよう',ev:{type:'tap',n:3,game:'puzzle',em:'🎈',title:'ミッション達成!',msg:'ここに裏話を書く。'}},
   {k:2,when:'いま',t:'いまの自分',e:'📱',g:'g4',ph:'ここにブルーアーカイブの写真',tx:'いまの自分は、ブルーアーカイブにハマりすぎています。',
    hint:'文章をたくさんタップしてみよう',ev:{type:'text',n:5,em:'💡',title:'かくれイベント',msg:'ここに意外な一面を書く。'}},
   {k:2,when:'いま',t:'いま頑張っていること',e:'📝',g:'g5',ph:'ここに写真',tx:'・志望理由書を書くこと<br>・3Dのこと<br>・行きたい会社の情報を調べること<br>・大学に入学したらインターンに入りたいので、その情報を集めること',
    hint:'写真をダブルタップしてみよう',ev:{type:'tap',n:2,game:'run',em:'💪',title:'ミッション達成!',msg:'ここに頑張る理由を書く。'}},
   {k:3,when:'これから',t:'これから頑張っていきたいこと',e:'🎯',g:'g3',ph:'ここにイメージ写真',
    tx:'・大学のインターンシップに行って、社会経験や仕事の経験を積む<br>・講義でしっかり単位を取る<br>・3Dモデリングやアニメーションを、仕事で使えるようになるまで練習する<br>・資格をちゃんと取る<br>・プログラミングをできるようにする<br>・絵を描けるようにする',
    ev:[]},
   {k:3,when:'数年後',t:'こうなっていたい自分',e:'🎓',g:'g1',ph:'ここにイメージ写真',tx:'ここに数年後の自分の姿を書く。',
    hint:'写真を3回タップしてみよう',ev:{type:'tap',n:3,game:'quiz',em:'📝',title:'クイズ全問正解!',msg:'ここにクイズのあとのメッセージを書く。'}},
   {k:3,when:'数年後',t:'挑戦してみたいこと',e:'🕹️',g:'g5',ph:'ここに東京ゲームショウのイメージ写真',tx:'3Dゲームを作って、東京ゲームショウに出展します。',
    hint:'写真をじーっと押してみよう',ev:{type:'holdp',game:'memory',em:'🃏',title:'神経衰弱クリア!',msg:'ここに挑戦への気持ちを書く。'}},
   {k:3,when:'いつか',t:'行ってみたい場所',e:'✈️',g:'g2',ph:'ここに北海道・ソウルの写真',tx:'北海道とソウルに行ってみたいです。',
    ev:{type:'spot',x:'18%',y:'30%',game:'tapchal',em:'⚡',title:'タップチャレンジ成功!',msg:'ここにその場所へ行きたい理由を書く。'}},
   {k:3,when:'いつか',t:'身につけたい力',e:'💪',g:'g3',ph:'ここにイメージ写真',tx:'筋力と、3Dの技術を身につけたいです。',
    hint:'文章を4回タップしてみよう',ev:{type:'text',n:4,game:'janken',em:'✌️',title:'じゃんけん勝利!',msg:'ここに力をつけたい理由を書く。'}},
   {k:3,when:'ずっと先の未来',t:'将来の夢',e:'🎬🌏',g:'g3',ph:'ここにイメージ写真',tx:'好きな作品の3Dアニメーションを作って盛り上げ、世界の人々に「楽しい」「面白い」と思ってもらいたいです。',
    ev:{type:'spot',x:'50%',y:'14%',em:'🚀',title:'かくれイベント',msg:'ここに本当はこうなりたい、という夢を書く。'}}
  ];
  var FINAL={em:'🏆',title:'全部見つけたね!',msg:'ここに最後のメッセージを書く(みんなへの一言、感謝の言葉など)。'};
  // ===== ここから下は仕組み =====
  var G={g1:'--p1,--p2',g2:'--p3,--p4',g3:'--p5,--p6',g4:'--p4,--p5',g5:'--p2,--p6',g6:'--p1,--p3'};
  var NAME={1:'PAST 過去',2:'PRESENT 現在',3:'FUTURE 未来'};
  var EV=[];
  E.forEach(function(d){d.evs=[].concat(d.ev);d.evs.forEach(function(v){v.id=EV.length;EV.push(v);});});
  var total=EV.length,count=0,found={},last=0;
  var tl=document.getElementById('tl'),ov=document.getElementById('ov'),cn=document.getElementById('counter');
  function upd(){cn.textContent=(count===total?'🎉':'🔍')+' かくれイベント '+count+'/'+total;}
  upd();
  function show(em,t,p){
    document.getElementById('m-em').textContent=em;
    document.getElementById('m-t').textContent=t;
    document.getElementById('m-p').textContent=p;
    ov.classList.add('on');
  }
  var mb=document.getElementById('m-b'),after=null;
  mb.onclick=function(){ov.classList.remove('on');if(after){var f=after;after=null;setTimeout(f,300);}};
  ov.onclick=function(e){if(e.target===ov)mb.onclick();};
  function burst(c,x,y){
    for(var i=0;i<14;i++){
      var s=document.createElement('span');s.className='b';s.textContent=c[i%c.length];
      s.style.left=x+'px';s.style.top=y+'px';
      s.style.setProperty('--dx',(Math.random()*300-150)+'px');
      s.style.setProperty('--dy',(Math.random()*300-200)+'px');
      document.body.appendChild(s);
      setTimeout((function(el){return function(){el.remove();};})(s),1400);
    }
  }
  function fire(i,x,y){
    var ev=EV[i];
    burst([ev.em,'✨','⭐'],x,y);
    show(ev.em,ev.title,ev.msg);
    if(!found[i]){
      found[i]=true;count++;upd();
      if(count===total)after=function(){show(FINAL.em,FINAL.title,FINAL.msg);};
    }
  }
  function multiTap(el,n,cb){
    var c=0,tm;
    el.addEventListener('click',function(e){
      c++;clearTimeout(tm);tm=setTimeout(function(){c=0;},700);
      if(c>=n){c=0;cb(e.clientX,e.clientY);}
    });
  }
  function openGame(done){
    var W=3,H=3,START=['🍎','🍋','🍎','🍇','🍎','🍋','🍋','🍇','🍇'];
    var B=START.slice(),sel=-1,busy=false,start=null;
    var gm=document.getElementById('gm'),grid=document.getElementById('gg'),msg=document.getElementById('gmsg');
    gm.classList.add('on');
    function left(){return B.filter(function(v){return v;}).length;}
    function say(t){msg.textContent=t;}
    function idle(){say('ミッション:スワイプで動かして全部消そう!(のこり'+left()+'こ)');}
    function matches(a){
      var m={},n=0,r,c,i;
      for(r=0;r<H;r++)for(c=0;c<=W-3;c++){i=r*W+c;if(a[i]&&a[i]===a[i+1]&&a[i]===a[i+2]){m[i]=m[i+1]=m[i+2]=1;n++;}}
      for(c=0;c<W;c++)for(r=0;r<=H-3;r++){i=r*W+c;if(a[i]&&a[i]===a[i+W]&&a[i]===a[i+2*W]){m[i]=m[i+W]=m[i+2*W]=1;n++;}}
      return n?m:null;
    }
    function render(out,hint){
      grid.innerHTML='';
      B.forEach(function(v,i){
        var d=document.createElement('div');
        d.className='tile'+(v?'':' empty')+(i===sel?' sel':'')+(out&&out[i]?' out':'')+(hint&&(hint[0]===i||hint[1]===i)?' hint':'');
        d.textContent=v;
        d.addEventListener('pointerdown',function(e){if(busy)return;start={i:i,x:e.clientX,y:e.clientY};});
        grid.appendChild(d);
      });
    }
    function move(a,b){
      if(!B[a]&&!B[b])return;
      var t=B[a];B[a]=B[b];B[b]=t;sel=-1;
      var m=matches(B);
      if(!m){render();idle();return;}
      busy=true;render(m);say('✨ そろった!');
      setTimeout(function(){
        for(var k in m)B[k]='';
        render();
        if(!left()){
          say('🎉 ミッション達成!');
          setTimeout(function(){gm.classList.remove('on');busy=false;done();},900);
        }else{busy=false;idle();}
      },600);
    }
    function tapSel(i){
      if(sel===i){sel=-1;render();return;}
      if(sel>=0){
        var dr=Math.abs(Math.floor(sel/W)-Math.floor(i/W)),dc=Math.abs(sel%W-i%W);
        if(dr+dc===1){move(sel,i);return;}
      }
      sel=B[i]?i:-1;render();
    }
    grid.onpointermove=function(e){
      if(!start||busy)return;
      var dx=e.clientX-start.x,dy=e.clientY-start.y;
      if(Math.max(Math.abs(dx),Math.abs(dy))<22)return;
      var i=start.i,r=Math.floor(i/W),c=i%W;
      if(Math.abs(dx)>Math.abs(dy))c+=dx>0?1:-1;else r+=dy>0?1:-1;
      start=null;
      if(r<0||r>=H||c<0||c>=W)return;
      move(i,r*W+c);
    };
    grid.onpointerup=grid.onpointercancel=function(){
      if(start&&!busy)tapSel(start.i);
      start=null;
    };
    document.getElementById('ghint').onclick=function(){
      if(busy)return;
      for(var i=0;i<B.length;i++){
        var nb=[];if(i%W<W-1)nb.push(i+1);if(i+W<B.length)nb.push(i+W);
        for(var n=0;n<nb.length;n++){
          var j=nb[n];if(!B[i]&&!B[j])continue;
          var t=B.slice(),x=t[i];t[i]=t[j];t[j]=x;
          if(matches(t)){render(null,[i,j]);return;}
        }
      }
      say('まずは自由に動かしてみよう!');
    };
    document.getElementById('greset').onclick=function(){if(busy)return;B=START.slice();sel=-1;render();idle();};
    document.getElementById('gquit').onclick=function(){if(!busy)gm.classList.remove('on');};
    render();idle();
  }
  function openRun(done){
    var rn=document.getElementById('rn'),cv=document.getElementById('rc'),ctx=cv.getContext('2d');
    cv.width=640;cv.height=360;ctx.setTransform(2,0,0,2,0,0);
    var W=320,H=180,GY=140,SP=200,PX=64,GOAL=5900;
    // 地形:f=平ら u=上り坂 d=下り坂 p=落とし穴 [種類,長さ,高さ]
    var P=[['f',500],['u',160,32],['f',80],['d',160,32],['f',220],['p',60],['f',300],['u',180,36],['f',60],['p',56],['f',120],['d',180,36],['f',260],['p',64],['f',200],['u',140,28],['d',140,28],['f',220],['p',60],['f',240],['u',160,32],['f',100],['p',60],['f',100],['d',160,32],['f',200],['p',64],['f',260],['u',200,40],['d',200,40],['f',200],['p',60],['f',240],['u',140,28],['d',140,28],['f',900]];
    var OBS=[380,930,1330,2150,2540,2910,3300,3990,4380,4920,5310,5800];
    var pcs=[],ex=0,el=0;
    P.forEach(function(p){
      var e1=el;if(p[0]==='u')e1=el-p[2];else if(p[0]==='d')e1=el+p[2];
      pcs.push({x0:ex,x1:ex+p[1],pit:p[0]==='p',e0:el,e1:e1});ex+=p[1];el=e1;
    });
    function ground(w){
      for(var i=0;i<pcs.length;i++){
        var p=pcs[i];
        if(w<p.x1){
          if(w<p.x0)break;
          return {y:GY+p.e0+(p.e1-p.e0)*(w-p.x0)/(p.x1-p.x0),pit:p.pit,end:p.x1};
        }
      }
      return {y:GY,pit:false,end:0};
    }
    var raf=0,st='ready',X=40,hearts=3,inv=0,py=GY,vy=0,grounded=true,crank=0,wheel=0,overAt=0,last=0;
    rn.classList.add('on');
    function reset(){X=40;hearts=3;inv=0;py=ground(X).y;vy=0;grounded=true;crank=0;}
    reset();
    function act(){
      var now=performance.now();
      if(st==='ready'||(st==='over'&&now-overAt>500)){reset();st='run';last=now;}
      else if(st==='run'&&grounded){vy=-420;grounded=false;}
    }
    function stop(){cancelAnimationFrame(raf);document.removeEventListener('keydown',kd);rn.classList.remove('on');}
    function kd(e){if(e.code==='Space'||e.code==='ArrowUp'){e.preventDefault();act();}}
    document.addEventListener('keydown',kd);
    cv.onpointerdown=function(e){e.preventDefault();act();};
    document.getElementById('rquit').onclick=stop;
    function hit(){hearts--;inv=1.2;if(hearts<=0){st='over';overAt=performance.now();}}
    function fall(){
      hearts--;
      if(hearts<=0){st='over';overAt=performance.now();return;}
      var g=ground(X);X=(g.pit?g.end:X)+30;
      g=ground(X);py=g.y;vy=0;grounded=true;inv=1.4;
    }
    function update(dt){
      X+=SP*dt;crank+=dt*(grounded?9:3);wheel+=dt*(grounded?12:4);
      var g=ground(X);
      if(grounded){
        if(g.pit){grounded=false;vy=0;}else py=g.y;
      }
      if(!grounded){
        vy+=1300*dt;py+=vy*dt;
        g=ground(X);
        if(g.pit){if(py>g.y+14){fall();return;}}
        else if(vy>=0&&py>=g.y){py=g.y;vy=0;grounded=true;}
      }
      if(inv>0)inv-=dt;
      else{
        for(var i=0;i<OBS.length;i++){
          var ox=OBS[i];
          if(X+13>ox+4&&X-13<ox+26&&py>ground(ox+14).y-22){hit();break;}
        }
      }
      if(st==='run'&&X>=GOAL){
        st='clear';
        setTimeout(function(){stop();done();},1000);
      }
    }
    function bike(x,y,tilt){
      ctx.save();ctx.translate(x,y);ctx.rotate(tilt);
      ctx.lineCap='round';ctx.lineJoin='round';
      function ln(a,b,c,d){ctx.beginPath();ctx.moveTo(a,b);ctx.lineTo(c,d);ctx.stroke();}
      var BB=[-3,-10],Hp=[-6,-27],R=[-14,-9],F=[14,-9],S=[-6,-24],Hd=[10,-24];
      function pedal(a){return [BB[0]+7*Math.cos(a),BB[1]+7*Math.sin(a)];}
      function leg(f,col){
        var l=12.5,dx=f[0]-Hp[0],dy=f[1]-Hp[1],d=Math.min(Math.sqrt(dx*dx+dy*dy),2*l-.5);
        var b=Math.atan2(dy,dx)-Math.acos(d/(2*l));
        var kx=Hp[0]+l*Math.cos(b),ky=Hp[1]+l*Math.sin(b);
        ctx.strokeStyle=col;ctx.lineWidth=4;
        ctx.beginPath();ctx.moveTo(Hp[0],Hp[1]);ctx.lineTo(kx,ky);ctx.lineTo(f[0],f[1]);ctx.stroke();
        ctx.fillStyle=col;ctx.fillRect(f[0]-1,f[1]-1,5,3);
      }
      [R,F].forEach(function(w){
        ctx.strokeStyle='#2b2a33';ctx.lineWidth=2.5;ctx.beginPath();ctx.arc(w[0],w[1],9,0,6.2832);ctx.stroke();
        ctx.strokeStyle='#8a8796';ctx.lineWidth=1;
        for(var k=0;k<3;k++){var a=wheel+k*Math.PI/3,c=Math.cos(a)*9,s=Math.sin(a)*9;ln(w[0]-c,w[1]-s,w[0]+c,w[1]+s);}
      });
      leg(pedal(crank+Math.PI),'#7a7688');
      ctx.strokeStyle='#e8745a';ctx.lineWidth=2.5;
      ln(R[0],R[1],BB[0],BB[1]);ln(BB[0],BB[1],S[0],S[1]);ln(S[0],S[1],Hd[0],Hd[1]);
      ln(BB[0],BB[1],Hd[0],Hd[1]);ln(Hd[0],Hd[1],F[0],F[1]);ln(R[0],R[1],S[0],S[1]);ln(Hd[0],Hd[1],9,-29);
      ctx.strokeStyle='#2b2a33';ctx.lineWidth=3;ln(-9,-25,-3,-25);
      leg(pedal(crank),'#2b2a33');
      ctx.strokeStyle='#4aa3df';ctx.lineWidth=6;ln(Hp[0],Hp[1],3,-38);
      ctx.lineWidth=3;ln(3,-38,10,-29);
      ctx.fillStyle='#ffd7b5';ctx.beginPath();ctx.arc(5,-45,5,0,6.2832);ctx.fill();
      ctx.fillStyle='#f08a5d';ctx.beginPath();ctx.arc(5,-46,5.5,3.14159,6.2832);ctx.fill();
      ctx.restore();
    }
    function center(t1,t2){
      ctx.fillStyle='rgba(0,0,0,.45)';ctx.fillRect(0,0,W,H);
      ctx.fillStyle='#fff';ctx.textAlign='center';
      ctx.font='bold 22px system-ui,sans-serif';ctx.fillText(t1,W/2,82);
      ctx.font='14px system-ui,sans-serif';ctx.fillText(t2,W/2,108);
    }
    function draw(){
      var g=ctx.createLinearGradient(0,0,0,GY);g.addColorStop(0,'#aee4ff');g.addColorStop(1,'#eaf8ff');
      ctx.fillStyle=g;ctx.fillRect(0,0,W,H);
      ctx.textAlign='left';ctx.font='26px serif';
      [[40,40],[170,66],[290,34]].forEach(function(c){ctx.fillText('☁️',((c[0]-X*.15)%360+360)%360-40,c[1]);});
      for(var sx=0;sx<W;sx+=3){
        var gg=ground(X+sx-PX);
        if(gg.pit){ctx.fillStyle='#3a3352';ctx.fillRect(sx,gg.y,3,H);}
        else{ctx.fillStyle='#8fd694';ctx.fillRect(sx,gg.y,3,H);ctx.fillStyle='#6fbf78';ctx.fillRect(sx,gg.y,3,5);}
      }
      ctx.textAlign='left';ctx.font='28px serif';
      OBS.forEach(function(ox,i){
        var x=PX+ox-X;
        if(x>-40&&x<W+10)ctx.fillText(i%2?'🚧':'🪨',x,ground(ox+14).y+4);
      });
      var fx=PX+GOAL-X;
      if(fx<W+30){ctx.font='34px serif';ctx.fillText('🏁',fx,ground(GOAL).y+4);}
      if(!(inv>0&&Math.floor(inv*10)%2)){
        var tilt=grounded?Math.atan((ground(X+10).y-ground(X-10).y)/20):Math.max(-.5,Math.min(.6,vy/700));
        bike(PX,py,tilt);
      }
      ctx.fillStyle='#2b2a33';ctx.font='bold 14px system-ui,sans-serif';
      ctx.textAlign='left';ctx.fillText('のこり '+Math.max(0,Math.ceil((GOAL-X)/SP))+' びょう',8,18);
      ctx.textAlign='right';ctx.fillText('❤'.repeat(Math.max(0,hearts))+'♡'.repeat(3-Math.max(0,hearts)),W-8,18);
      if(st==='ready')center('タップでスタート!','ジャンプして、障害物と穴をよけよう');
      if(st==='over')center('ざんねん…','タップでもう一回!');
      if(st==='clear')center('🎉 ゴール!','ミッション達成!');
    }
    function loop(now){
      var dt=Math.min(.05,Math.max(0,(now-last)/1000));last=now;
      if(st==='run')update(dt);
      draw();
      raf=requestAnimationFrame(loop);
    }
    last=performance.now();raf=requestAnimationFrame(loop);
  }
  // ===== かんたんクイズ・ミニゲーム =====
  var QUIZ=[ // ← 自分の問題に書き換える(a=正解の番号。0から数える)
    {q:'わたしの好きなゲームは?',o:['マインクラフト','ブルーアーカイブ','スプラトゥーン'],a:1},
    {q:'わたしの好きなキャラは?',o:['砂狼シロコ','聖園ミカ','小鳥遊ホシノ'],a:2},
    {q:'わたしが最近ハマってることは?',o:['推し活(ブルアカ)','料理','ランニング'],a:0}
  ];
  var MG={},mgGen=0,mgTimer=0;
  var mg=document.getElementById('mg'),mgb=document.getElementById('mgb'),mgt=document.getElementById('mgt'),mgp=document.getElementById('mgp');
  function mgOpen(t,p){mgGen++;mgt.textContent=t;mgp.textContent=p;mgb.innerHTML='';mg.classList.add('on');return mgGen;}
  function mgClose(){mgGen++;clearTimeout(mgTimer);mgb.innerHTML='';mg.classList.remove('on');}
  document.getElementById('mgq').onclick=mgClose;
  function mgWin(done,m){mgp.textContent=m;mgTimer=setTimeout(function(){mgClose();done();},1000);}
  function btn(label,cls,fn){var b=document.createElement('button');b.className=cls;b.textContent=label;b.onclick=fn;return b;}

  MG.quiz=function(done){
    var g=mgOpen('📝 わたしクイズ','全問正解でクリア!'),n=0;
    function show(){
      mgb.innerHTML='';
      var d=QUIZ[n],q=document.createElement('div');
      q.className='qq';q.textContent='第'+(n+1)+'問 / '+QUIZ.length+':'+d.q;mgb.appendChild(q);
      d.o.forEach(function(t,k){
        mgb.appendChild(btn(t,'opt',function(){
          if(g!==mgGen)return;
          if(k!==d.a){mgp.textContent='❌ おしい!もう一回えらんでね';return;}
          n++;mgp.textContent='⭕ 正解!';
          if(n>=QUIZ.length){mgb.innerHTML='';mgWin(done,'🎉 全問正解!');}
          else setTimeout(function(){if(g===mgGen){mgp.textContent='全問正解でクリア!';show();}},600);
        }));
      });
    }
    show();
  };

  MG.memory=function(done){
    var g=mgOpen('🃏 神経衰弱','同じ絵を2枚そろえよう(3ペア)');
    var A=['🍎','🍎','🍋','🍋','🍇','🍇'].sort(function(){return Math.random()-.5;}),open=[],lock=false,found=0,cards=[];
    var grid=document.createElement('div');grid.className='mgrid';mgb.appendChild(grid);
    A.forEach(function(v,i){
      var c=btn('❓','tile',function(){
        if(g!==mgGen||lock||c.dataset.on||open.indexOf(i)>=0)return;
        c.textContent=v;open.push(i);
        if(open.length<2)return;
        var x=open[0],y=open[1];lock=true;
        if(A[x]===A[y]){
          cards[x].dataset.on=1;cards[y].dataset.on=1;found++;open=[];lock=false;
          if(found===3)mgWin(done,'🎉 ぜんぶそろった!');
        }else setTimeout(function(){
          if(g!==mgGen)return;
          cards[x].textContent='❓';cards[y].textContent='❓';open=[];lock=false;
        },700);
      });
      cards.push(c);grid.appendChild(c);
    });
  };

  MG.tapchal=function(done){
    var g=mgOpen('⚡ タップチャレンジ','10秒間に15回タップしよう!'),n=0,left=10,run=false,iv=0;
    var info=document.createElement('div');info.className='qq';
    function upd(){info.textContent='のこり '+left+' びょう / '+n+' 回';}
    var b=btn('👆','big',function(){
      if(g!==mgGen)return;
      if(!run){
        run=true;n=0;left=10;mgp.textContent='10秒間に15回タップしよう!';
        iv=setInterval(function(){
          if(g!==mgGen){clearInterval(iv);return;}
          left--;upd();
          if(left<=0){clearInterval(iv);run=false;mgp.textContent='おしい!あと'+(15-n)+'回!もう一回やってみよう';}
        },1000);
      }
      n++;upd();
      if(n>=15){clearInterval(iv);run=false;mgWin(done,'🎉 '+n+'回タップ!クリア!');}
    });
    upd();mgb.appendChild(info);mgb.appendChild(b);
  };

  MG.janken=function(done){
    var g=mgOpen('✌️ じゃんけん勝負','1回勝ったらクリア!'),won=false;
    var H=['✊','✌️','✋'],res=document.createElement('div');
    res.className='qq';res.textContent='さいしょはグー!';mgb.appendChild(res);
    var row=document.createElement('div');row.className='mgrid';mgb.appendChild(row);
    H.forEach(function(h,k){
      row.appendChild(btn(h,'tile',function(){
        if(g!==mgGen||won)return;
        var c=Math.floor(Math.random()*3),r=(k-c+3)%3,t='あなた '+H[k]+' vs '+H[c]+' あいて';
        if(r===2){won=true;res.textContent=t;mgWin(done,'🎉 勝ったー!');}
        else res.textContent=t+(r===0?' → あいこ!もう一回':' → まけ…もう一回!');
      }));
    });
  };

  function launch(i,x,y){
    var g=EV[i].game,cb=function(){fire(i,x,y);};
    if(g==='run')openRun(cb);else if(g==='puzzle')openGame(cb);
    else if(MG[g])MG[g](cb);else cb();
  }

  E.forEach(function(d,i){
    if(d.k!==last){
      last=d.k;
      var c=document.createElement('div');c.className='chip k'+d.k;c.textContent=NAME[d.k];tl.appendChild(c);
    }
    var s=document.createElement('section');s.className='item k'+d.k;
    var gr=G[d.g].split(',');
    s.innerHTML='<div class="when">'+d.when+'</div><h2>'+d.t+'</h2>'+
      '<div class="photo" style="background:linear-gradient(135deg,var('+gr[0]+'),var('+gr[1]+'))"><span>'+d.e+'</span><small>'+d.ph+'</small></div>'+
      '<div class="text"><p>'+d.tx+'</p></div>'+(d.hint?'<div class="hint">💡 '+d.hint+'</div>':'');
    tl.appendChild(s);
    var photo=s.querySelector('.photo');
    d.evs.forEach(function(ev){
    var i=ev.id;
    var go=function(x,y){launch(i,x,y);};
    function holdOn(el){
      var lp;
      el.addEventListener('contextmenu',function(e){e.preventDefault();});
      el.addEventListener('pointerdown',function(e){var x=e.clientX,y=e.clientY;lp=setTimeout(function(){go(x,y);},700);});
      ['pointerup','pointerleave','pointercancel'].forEach(function(n){el.addEventListener(n,function(){clearTimeout(lp);});});
    }
    if(ev.type==='spot'){
      var b=document.createElement('button');b.className='spot';b.setAttribute('aria-label','かくしスポット');
      b.style.left='calc('+ev.x+' - 18px)';b.style.top='calc('+ev.y+' - 18px)';
      b.addEventListener('click',function(e){e.stopPropagation();go(e.clientX,e.clientY);});
      photo.appendChild(b);
    }else if(ev.type==='tap'){multiTap(photo,ev.n,go);}
    else if(ev.type==='text'){multiTap(s.querySelector('.text'),ev.n,go);}
    else if(ev.type==='hold'){holdOn(s.querySelector('h2'));}
    else if(ev.type==='holdp'){holdOn(photo);}
    });
  });
})();
