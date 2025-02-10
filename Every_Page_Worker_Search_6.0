// ==UserScript==
// @name        Every Page Worker 💢💢 Search
// @namespace        http://tampermonkey.net/
// @version        6.0
// @description        「記事の編集・削除」でブログ全記事を開いて検索を実行
// @author        Ameba Blog User
// @match        https://blog.ameba.jp/ucs/entry/srventrylist*
// @icon        https://www.google.com/s2/favicons?sz=64&domain=ameba.jp
// @run-at        document-start
// @grant        none
// @updateURL        https://github.com/personwritep/Every_Page_Worker_Search/raw/main/Every_Page_Worker_Search.user.js
// @downloadURL        https://github.com/personwritep/Every_Page_Worker_Search/raw/main/Every_Page_Worker_Search.user.js
// ==/UserScript==


let ua=0;
let agent=window.navigator.userAgent.toLowerCase();
if(agent.indexOf('firefox') > -1){ ua=1; } // Firefoxの場合のフラッグ



let retry=0;
let interval=setInterval(wait_target, 10);
function wait_target(){
    retry++;
    if(retry>20){ // リトライ制限 20回 0.2sec
        clearInterval(interval); }
    let target=document.documentElement; // 監視 target
    if(target){
        clearInterval(interval);
        style_in(); }}


function style_in(){ // CSSを適用
    let style=
        '<style id="EPW">'+
        '#globalHeader, #ucsHeader, #ucsMainLeft h1, .l-ucs-sidemenu-area, .selection-bar { '+
        'display: none !important; } '+

        '#ucsContent { width: 930px !important; } '+
        '#ucsContent::before { display: none; } '+
        '#ucsMainLeft { width: 930px !important; padding: 0 15px !important; } '+

        '#entrySort { margin-bottom: 2px; } '+
        '#nowMonth { color: #000; } '+
        '#entryListEdit form { display: flex; flex-direction: column; } '+
        '#entrySort { order: -2; } '+
        '.pagingArea { order: -1; margin-bottom: -33px; position:unset !important; } '+
        '.pagingArea a { border: 1px solid #888; } '+
        '.pagingArea .active{ border: 2px solid #0066cc; } '+
        '.pagingArea a, .pagingArea .active, .pagingArea .disabled { font-size: 14px; line-height: 23px; } '+
        '#sorting { margin: 36px 0 4px; padding: 2px 10px; height: 80px; background: #ddedf3; } '+
        '#sorting select, #sorting ul { display: none; } '+

        '#entryList .status-text { right: 374px !important; } '+
        '#entryList .entry-info .date { right: 260px !important; } '+
        '#entryList .actions { width: 240px; } '+

        '#div0 { position: relative; color: #333; font-size: 14px; margin: 0; }'+
        '#div1 { position: relative; color: #000; font-size: 14px; margin: 0 -10px 0 0; }'+
        'input { font-family: meiryo; font-size: 16px; }'+
        '.ep_wrap { display: inline-block; position: relative; }'+
        '.ep_help { position: absolute; font-size: 16px; width: fit-content; '+
        'padding: 2px 15px 0; color: #fff; background: #000; display: none; }'+

        '#scan_button { padding: 2px 15px 0; margin: 7px 40px 7px 0; width: 210px; }'+
        '#reset { padding: 2px 0 0; margin-right: 30px; width: 120px; }'+
        '#wrap_exp { margin-right: 10px; }'+
        '#export { padding: 2px 0 0; margin: 7px 0 2px; width: 66px; }'+
        '#h_exp { top: -30px; left: -330px; }'+
        '#wrap_exp:hover #h_exp { display: block; }'+
        '#wrap_imp { margin-right: 40px; }'+
        '#import_face { padding: 2px 0 0; margin: 7px 0; width: 66px; }'+
        '#import { display: none; }'+
        '#h_imp { top: -30px; left: -300px; }'+
        '#wrap_imp:hover #h_imp { display: block; }'+
        '#wrap_reg { margin-right: 30px; }'+
        '#regex { padding: 2px 0 0; margin: 7px 0; width: 100px; }'+
        '#h_reg { top: -30px; left: -240px; }'+
        '#wrap_reg:hover #h_reg { display: block; }'+
        '#wrap_win { margin-right: 25px; }'+
        '#w_size { padding: 2px 4px 0; width: 26px; }'+
        '#h_win { top: -37px; left: -525px; margin-right: -20px; }'+
        '#wrap_win:hover #h_win { display: block; }'+
        '#help_page { display: inline-block; font: bold 16px/23px Meiryo; '+
        'height: 19px; padding: 0 2px; border: 1px solid #aaa; '+
        'border-radius: 30px; background: #fff; cursor: pointer; }'+
        '#h_scan { top: -30px; left: -10px; width: 780px; }'+

        '.scan_num { display: inline-block; margin-right: 5px; }'+
        '#search1, #search2 { width: 185px; height: 30px; padding: 2px 0 0 6px; '+
        'outline: none; }'+
        '#search1:-webkit-autofill, #search2:-webkit-autofill { border: thin solid #aaa; '+
        'box-shadow: inset 0 0 0 20px white; }'+
        '#set1, #set2 { padding: 2px 3px 0; margin: 0 4px 0 -1px; }'+
        '#num1, #num2 { padding: 2px 2px 0 6px; width: 50px; }'+
        '#open1, #open2 { padding: 2px 3px 0; margin-left: -1px; }'+
        '#open1 { margin-right: 5px; }'+
        '#h_set, #h_edit { top: -74px; left: -10px; width: 780px; }'+
        '.ch1, .ch2 { font: 16px/28px Meiryo; color: #0277bd; opacity: 0; }'+
        '.ch1 { margin-left: 6px; }'+
        '.ch2 { margin-left: 2px; }'+
        '</style>';

    if(!document.querySelector('#EPW')){
        document.documentElement.insertAdjacentHTML('beforeend', style); }

} // style_in()




window.addEventListener('load', function(){ // 親ウインドウで動作

    let drive_mode; // ページ更新時の動作モード
    let blogDB={}; //「対象記事のID/チェックフラグ または内容」の記録配列

    let entry_id_DB; // ID検索用の配列
    let hit1; // 検索❶ がヒットした件数
    let hit2; // 検索❷ がヒットした件数

    let search_word1;
    let search_word2;
    let regex_search; // 通常検索: 0　正規表現検索：1

    let entry_id;
    let entry_target;
    let list_bar;
    let editor_flg;

    let next_target; // ページ内の次の対象記事
    let new_win;
    let link_target;
    let editor_iframe;
    let iframe_doc;

    let win_option; // サブウインドウの表示サイズと位置


    let read_json=localStorage.getItem('EPW_DB_back'); // ローカルストレージ 保存名
    blogDB=JSON.parse(read_json);
    if(blogDB==null || blogDB.length<4){
        blogDB=[['epw00000000', 's', 0], ['', 0, 0], ['', 0, 0],
                ["top=20, left=0, width=600, height=99", 0, 0]]; }
    drive_mode=blogDB[0][1]; // 起動時に動作フラグを取得
    regex_search=blogDB[0][2]; // 正規検索のフラグを取得
    search_word1=blogDB[1][0]; // 検索❶ の検索文字を取得
    search_word2=blogDB[2][0]; // 検索❷ の検索文字を取得
    win_option=blogDB[3][0]; // サブウインドウの配置を取得
    blogDB[0][1]='s'; // リロード時等のためにリセット
    let write_json=JSON.stringify(blogDB);
    localStorage.setItem('EPW_DB_back', write_json); // ローカルストレージ 保存


    reg_set();

    function reg_set(){
        entry_id_DB=[]; // リセット
        hit1=0;
        hit2=0;
        for(let k=4; k<blogDB.length; k++){
            entry_id_DB[k]=blogDB[k][0]; // ID検索用の配列を作成
            if(blogDB[k][1]=='1'){
                hit1 +=1; } // 検索❶ がヒットした件数
            if(blogDB[k][2]=='1'){
                hit2 +=1; }}} // 検索❷ がヒットした件数



    entry_id=document.querySelectorAll('input[name="entry_id"]');
    entry_target=document.querySelectorAll('.entry-item .entry');
    list_bar=document.querySelectorAll('#entryList .entry-item');


    control_pannel(drive_mode);
    mark_display();
    hit_display();




    function control_pannel(dm){
        let insert_div=
            '<div id="div0">'+
            '<input id="scan_button" type="submit">'+
            '<input id="reset" type="submit" value="Reset All Data">'+
            '<div id="wrap_exp" class="ep_wrap">'+
            '<input id="export" type="submit" value="Export">'+
            '<div id="h_exp" class="ep_help">'+
            '検索結果のデータを外部ファイルに保存します ▼</div>'+
            '</div>'+
            '<div id="wrap_imp" class="ep_wrap">'+
            '<input id="import_face" type="submit" value="Import">'+
            '<input id="import" type="file">'+
            '<div id="h_imp" class="ep_help">'+
            'EPW(n).jsonファイルを指定してください ▼</div>'+
            '</div>'+
            '<div id="wrap_reg" class="ep_wrap">'+
            '<input id="regex" type="submit" value="Normal">'+
            '<div id="h_reg" class="ep_help">'+
            '通常検索/正規表現検索を切替えます ▼</div>'+
            '</div>'+
            '<div id="wrap_win" class="ep_wrap">'+
            '<input id="w_size" type="submit" value="◪">'+
            '<div id="h_win" class="ep_help">'+
            '小ウインドウ（位置・サイズ）/ 編集ウインドウ（位置）を指定します ▼</div>'+
            '</div>'+
            '<span id="help_page">？</span>'+
            '<div id="h_scan" class="ep_help">'+
            '現在表示しているページ以降の全記事を検索します　'+
            'このボタンで検索を一旦停止・再開できます</div>'+
            '</div>'+

            '<div id="div1">'+
            '<span class="scan_num">タイトル ❶</span>'+
            '<input id="search1" type="search" placeholder="記事タイトル　検索文字">'+
            '<input id="set1" type="submit" value="Set">'+
            '<input id="num1" type="number" min="1">'+
            '<input id="open1" type="submit" value="Edit">'+
            '<span class="scan_num">本文 ❷</span>'+
            '<input id="search2" type="search" placeholder="記事本文　検索文字">'+
            '<input id="set2" type="submit" value="Set">'+
            '<input id="num2" type="number" min="1">'+
            '<input id="open2" type="submit" value="Edit">'+
            '<div id="h_set" class="ep_help">'+
            '「Set」：検索語を書換えた時に 変更を確定します　'+
            '変更した側の検索データはリセットされます</div>'+
            '<div id="h_edit" class="ep_help">'+
            '「Edit」：左の枠にセットした検索ヒット番号の記事を 編集画面に開きます</div>'+
            '</div>';

        let box=document.querySelector('#sorting');
        if(!box.querySelector('#div0')){
            box.insertAdjacentHTML('beforeend', insert_div); }




        let button1=document.querySelector('#scan_button');
        if(dm=='s'){
            button1.value='全記事へ検索を開始　▶'; }
        else if(dm=='c'){
            button1.value='　検索を一旦停止　　❚❚'; }
        else if(dm=='e'){
            button1.value='検索が全て終了しました'; }

        button1.addEventListener('click', function(e){
            e.preventDefault();
            if(e.ctrlKey){
                start_stop(1); } // ページの途中から連続処理スタート
            else{
                start_stop(0); }}, false);


        function start_stop(n){
            if(drive_mode=='s'){ // 最初の起動直後
                let conf_str=
                    '　　 🔴　このページ以降の記事に連続した検索を実行します\n\n'+
                    '　　　　  停止ボタンのクリックで検索停止/検索再開ができます';
                let ok=confirm(conf_str);
                if(ok){
                    drive_mode='c'; // ページ内の連続処理
                    button1.value='　検索を一旦停止　　❚❚';
                    if(n==0){
                        next(0); }
                    else{
                        alert('　処理を開始する記事を左クリックしてください');
                        clicked_item(); }}}

            else if(drive_mode=='c'){ // 連続動作状態の場合
                drive_mode='p'; // クリックされたら「p」停止モード
                button1.value='　検索を再開する　　▶'; }

            else if(drive_mode=='p'){ // 動作停止状態の場合
                drive_mode='c'; // クリックされたら連続動作を再開
                button1.value='　検索を一旦停止　　❚❚';
                open_win(next_target); }

            function clicked_item(){
                let entry_item=document.querySelectorAll('.entry-item');
                for(let k=0; k<entry_item.length; k++){
                    entry_item[k].onclick=function(e){
                        e.preventDefault();
                        e.stopImmediatePropagation();
                        next(k); }}}
        } // start_stop()


        if(dm=='c'){ // ページを開いた時に「c」は連続動作
            setTimeout(next(0), 200); } // 「c」連続動作はぺージ遷移時 0.2sec で自動実行 ⭕
        else if(dm=='e'){ // 「e」は終了
            button1.style.pointerEvents='none'; }


        let h_scan=document.querySelector('#h_scan');
        button1.onmouseover=function(){
            h_scan.style.display='block'; }

        button1.onmouseleave=function(){
            h_scan.style.display='none'; }




        let button2=document.querySelector('#reset');
        button2.onclick=function(e){
            e.preventDefault();
            let yes=window.confirm("　🔴 全ての検索データを初期化します");
            if(yes){
                blogDB=[['epw00000000', 's', regex_search],
                        [search_word1, 0, 0], [search_word2, 0, 0], [win_option, 0, 0]];
                let write_json=JSON.stringify(blogDB);
                localStorage.setItem('EPW_DB_back', write_json); // ローカルストレージ保存
                scan_disp();
                hit_display_clear();
                scanline_clear();
                drive_mode='s';
                button1.value='全記事へ検索を開始　▶';
                button2.value='〔　〕';
                setTimeout(()=>{
                    button2.value='Reset All Data'; }, 2000); }}



        let button3=document.querySelector('#export');
        button3.onclick=function(e){
            e.preventDefault();
            let write_json=JSON.stringify(blogDB);
            let blob=new Blob([write_json], {type: 'application/json'});
            let a_elem=document.createElement('a');
            a_elem.href=URL.createObjectURL(blob);
            a_elem.download='EPW.json'; // 保存ファイル名
            if(ua==1){
                a_elem.target='_blank';
                document.body.appendChild(a_elem); }
            a_elem.click();
            if(ua==1){
                document.body.removeChild(a_elem); }
            URL.revokeObjectURL(a_elem.href); }



        let button4_face=document.querySelector('#import_face');
        button4_face.onclick=function(e){
            e.preventDefault();
            button4.click(); }

        let button4=document.querySelector('#import');
        button4.addEventListener("change", function(){
            if(!(button4.value)) return; // ファイルが選択されない場合
            let file_list=button4.files;
            if(!file_list) return; // ファイルリストが選択されない場合
            let file=file_list[0];
            if(!file) return; // ファイルが無い場合

            let file_reader=new FileReader();
            file_reader.readAsText(file);
            file_reader.onload=function(){
                if(file_reader.result.slice(0, 15)=='[["epw00000000"'){ // EPW.jsonの確認
                    let data_in=JSON.parse(file_reader.result);
                    blogDB=data_in; // 読込み上書き処理
                    let write_json=JSON.stringify(blogDB);
                    localStorage.setItem('EPW_DB_back', write_json); // ローカルストレージ 保存

                    scan_disp();
                    regex_search=blogDB[0][2]; // 正規検索のフラグを取得
                    if(regex_search==0){
                        button_regex.value="Normal"; }
                    else if(regex_search==1){
                        button_regex.value="💢\u2006RegExp"; }
                    search_word1=blogDB[1][0]; // 検索❶ の検索文字を取得
                    input5.value=search_word1;
                    search_word2=blogDB[2][0]; // 検索❷ の検索文字を取得
                    input8.value=search_word2;
                    win_option=blogDB[3][0]; // サブウインドウの配置を取得
                    hit_display_clear();
                    hit_display();
                    scanline_clear();
                    drive_mode='s';
                    button1.value='全記事へ検索を開始　▶'; }
                else{
                    alert("   ⛔ 不適合なファイルです  EPW.json ファイルを選択してください");}};});



        let button_regex=document.querySelector('#regex');
        if(regex_search==0){
            button_regex.value="Normal"; }
        else if(regex_search==1){
            button_regex.value="💢\u2006RegExp"; }

        button_regex.onclick=function(e){
            e.preventDefault();
            let yes=window.confirm("　🔴 全ての検索データを初期化します");
            if(yes){
                if(regex_search==0){
                    regex_search=1;
                    button_regex.value="💢\u2006RegExp"; }
                else if(regex_search==1){
                    regex_search=0;
                    button_regex.value="Normal"; }

                blogDB=[['epw00000000', 's', regex_search],
                        [search_word1, 0, 0], [search_word2, 0, 0], [win_option, 0, 0]];
                let write_json=JSON.stringify(blogDB);
                localStorage.setItem('EPW_DB_back', write_json); // ローカルストレージ保存
                scan_disp();
                hit_display_clear();
                scanline_clear();
                drive_mode='s';
                button1.value='全記事へ検索を開始　▶';
                document.querySelector('#reset').value='〔　〕';
                setTimeout(()=>{
                    button2.value='Reset All Data'; }, 2000); }}



        let w_size=document.querySelector('#w_size');
        w_size.onclick=function(e){
            e.preventDefault();
            let tw=window.open('', 'tmp_window', win_option); // 🔲
            tw.document.write(
                'サブウインドウの設定<br>'+
                '<input type="submit" style="display: block; margin: 6px auto; '+
                'padding: 2px 10px 0;" onclick="window.close()" '+
                'value="小ウインドウの位置・サイズを設定\n編集ウインドウは左上の位置を設定">'+
                '<style>body{ background: #cbdce3; font-size: 15px; }</style>');

            tw.onbeforeunload=function(){
                win_option='top='+tw.screenY+', left='+tw.screenX+', width='+
                    tw.innerWidth+', height='+tw.innerHeight; // 🔲
                blogDB[3]=[win_option, 0, 0];
                let write_json=JSON.stringify(blogDB);
                localStorage.setItem('EPW_DB_back', write_json); }} // ローカルストレージ保存



        let input5=document.querySelector('#search1');
        input5.value=search_word1;
        input5.onkeydown=function(event){
            if(event.keyCode==13){
                event.preventDefault();
                button5.focus(); }}

        input5.onchange=function(){
            if(input5.value!=search_word1){
                if(ua==1){
                    button5.style.boxShadow='rgb(255, 136, 0) 0 0 0 2px'; }
                else{
                    button5.style.boxShadow='inset 0 0 0 20px rgba(255, 136, 0, 0.4)';}}
            else{
                button5.style.boxShadow='none'; }}

        let button5=document.querySelector('#set1');
        button5.onclick=function(event){
            event.preventDefault();
            if(input5.value!=search_word1){
                if(hit1!=0){
                    let yes=window.confirm("　🔴 検索❶ のデータを初期化します");
                    if(yes){
                        for(let k=4; k<blogDB.length; k++){
                            if(blogDB[k][1]==1){
                                blogDB[k][1]=0; }}
                        scan_disp();
                        hit_display_clear();
                        hit_display(); }
                    else{
                        input5.value=search_word1; }}
                search_word1=input5.value; // 検索❶の検索語を登録
                blogDB[1]=[input5.value, 0, 0]; // 検索❶の検索語をストレージに記録
                let write_json=JSON.stringify(blogDB);
                localStorage.setItem('EPW_DB_back', write_json); } // ローカルストレージ保存
            scanline_clear();
            drive_mode='s';
            button1.value='全記事へ検索を開始　▶';
            button5.style.boxShadow='none';
            button5.blur(); }


        let h_set=document.querySelector('#h_set');
        button5.onmouseover=function(){
            h_set.style.display='block'; }

        button5.onmouseleave=function(){
            h_set.style.display='none'; }



        let button6=document.querySelector('#num1');
        let button7=document.querySelector('#open1');
        button7.onclick=function(e){
            e.preventDefault();
            let hit1_DB=[]; // hit1 の entry_id の配列
            if(hit1>0){
                for(let k=4; k<blogDB.length; k++){
                    if(blogDB[k][1]=='1'){
                        hit1_DB.push(blogDB[k][0]); }}

                if(button6.value>0){
                    let open_id=hit1_DB[button6.value -1];
                    let pass=
                        'https://blog.ameba.jp/ucs/entry/srventryupdateinput.do?id='+ open_id;
                    let options=blogDB[3][0].split(',');
                    let win_option_e=options[0]+', '+options[1]+', width=1020, height=900';
                    window.open(pass, button6.value, win_option_e); }}}


        let h_edit=document.querySelector('#h_edit');
        button7.onmouseover=function(){
            h_edit.style.display='block'; }

        button7.onmouseleave=function(){
            h_edit.style.display='none'; }



        let input8=document.querySelector('#search2');
        input8.value=search_word2;
        input8.onkeydown=function(event){
            if(event.keyCode==13){
                event.preventDefault();
                button8.focus(); }}

        input8.onchange=function(){
            if(input8.value!=search_word2){
                if(ua==1){
                    button8.style.boxShadow='rgb(255, 136, 0) 0 0 0 2px'; }
                else{
                    button8.style.boxShadow='inset 0 0 0 20px rgba(255, 136, 0, 0.4)';}}
            else{
                button8.style.boxShadow='none'; }}

        let button8=document.querySelector('#set2');
        button8.onclick=function(event){
            event.preventDefault();
            if(input8.value!=search_word2){
                if(hit2!=0){
                    let yes=window.confirm("　🔴 検索❷ のデータを初期化します");
                    if(yes){
                        for(let k=4; k<blogDB.length; k++){
                            if(blogDB[k][2]==1){
                                blogDB[k][2]=0; }}
                        scan_disp();
                        hit_display_clear();
                        hit_display(); }
                    else{
                        input8.value=search_word2; }}
                search_word2=input8.value; // 検索❷の検索語を登録
                blogDB[2]=[input8.value, 0, 0]; // 検索❷の検索語をストレージに記録
                let write_json=JSON.stringify(blogDB);
                localStorage.setItem('EPW_DB_back', write_json); }// ローカルストレージ保存
            scanline_clear();
            drive_mode='s';
            button1.value='全記事へ検索を開始　▶';
            button8.style.boxShadow='none';
            button8.blur(); }


        button8.onmouseover=function(){
            h_set.style.display='block'; }

        button8.onmouseleave=function(){
            h_set.style.display='none'; }



        let button9=document.querySelector('#num2');
        let button10=document.querySelector('#open2');
        button10.onclick=function(e){
            e.preventDefault();
            let hit2_DB=[]; // hit2 の entry_id の配列
            if(hit2>0){
                for(let k=4; k<blogDB.length; k++){
                    if(blogDB[k][2]=='1'){
                        hit2_DB.push(blogDB[k][0]); }}

                if(button9.value>0){
                    let open_id=hit2_DB[button9.value -1];
                    let pass=
                        'https://blog.ameba.jp/ucs/entry/srventryupdateinput.do?id='+ open_id;
                    let options=blogDB[3][0].split(',');
                    let win_option_e=options[0]+', '+options[1]+', width=1020, height=900';
                    window.open(pass, button9.value, win_option_e); }}}


        button10.onmouseover=function(){
            h_edit.style.display='block'; }

        button10.onmouseleave=function(){
            h_edit.style.display='none'; }


        let help_page=document.querySelector('#help_page');
        help_page.onclick=function(){
            let url='https://ameblo.jp/personwritep/entry-12759864333.html';
            window.open(url, '_blank'); }


        scan_disp();

    } // control_pannel()



    function scan_disp(){
        reg_set();
        let button6=document.querySelector('#num1');
        button6.value=hit1;
        button6.max=hit1;
        if(hit1==0){
            button6.disabled=true; }
        else{
            button6.disabled=false; }
        let button9=document.querySelector('#num2');
        button9.value=hit2;
        button9.max=hit2;
        if(hit2==0){
            button9.disabled=true; }
        else{
            button9.disabled=false; }}


    function mark_display(){
        let actions=document.querySelectorAll('#entryList .actions');
        for(let k=0; k<actions.length; k++){
            let hit_mark='<span class="ch1">❶</span><span class="ch2">❷</span>';
            actions[k].insertAdjacentHTML('beforeend', hit_mark); }}


    function hit_display(){
        let actions=document.querySelectorAll('#entryList .actions');
        let ch1=document.querySelectorAll('.ch1');
        let ch2=document.querySelectorAll('.ch2');

        for(let k=0; k<actions.length; k++){
            let index=entry_id_DB.indexOf(entry_id[k].value);
            if(index!=-1){ // IDがblogDBに記録されていた場合
                if(blogDB[index][1]==1){
                    ch1[k].style.opacity='1'; }
                else{
                    ch1[k].style.opacity='0'; }
                if(blogDB[index][2]==1){
                    ch2[k].style.opacity='1'; }
                else{
                    ch2[k].style.opacity='0'; }}}}


    function hit_display_clear(){
        let ch1=document.querySelectorAll('.ch1');
        let ch2=document.querySelectorAll('.ch2');
        for(let k=0; k<ch1.length; k++){
            ch1[k].style.opacity='0';
            ch2[k].style.opacity='0'; }}


    function scanline_clear(){
        list_bar=document.querySelectorAll('#entryList .entry-item');
        for(let k=0; k<list_bar.length; k++){
            list_bar[k].style.background='none';
            list_bar[k].style.boxShadow='none'; }}




    function escapeRegExp(string){
        let reRegExp=/[\\^$.*+?()[\]{}|]/g;
        let reHasRegExp=new RegExp(reRegExp.source);
        return (string && reHasRegExp.test(string))
            ? string.replace(reRegExp, '\\$&')
        : string; }


    function next(x){ // xはページ内の記事index[0～length-1]
        entry_id=document.querySelectorAll('input[name="entry_id"]');
        if(entry_id.length >x){
            open_win(x); } // 投稿記事がある場合 open_win を開始
        else{
            next_call();}} // 投稿記事が無ければ 次ページをcall する


    function open_win(k){
        next_target=k; // 送信完了までは未処理とする

        let search_word1e;
        let search_word2e;
        if(regex_search==0){ // 通常検索 自動エスケープ処理
            search_word1e=escapeRegExp(search_word1);
            search_word2e=escapeRegExp(search_word2); }
        else if(regex_search==1){
            search_word1e=search_word1;
            search_word2e=search_word2; }

        new_win=Array(entry_target.length);
        link_target=Array(entry_target.length);
        link_target[k]='/ucs/entry/srventryupdateinput.do?id='+ entry_id[k].value;
        if(drive_mode=='c'){
            new_win[k]=window.open(link_target[k], k, win_option); // 🔲

            list_bar[k].style.boxShadow='inset 0 0 0 2px #03a9f4'; // リスト欄に青枠表示
            new_win[k].addEventListener('load', work, false); } // 子ウインドウの処理


        function work(){
            let editor_flg=new_win[k].document.querySelector('input[name="editor_flg"]');
            if(editor_flg.value=="5"){ // 最新版エディタの文書の場合のみ処理
                let interval=setInterval(find_iframe, 10); // iframe 読込み待機コード ⭕
                function find_iframe(){
                    let editor_iframe=new_win[k].document.querySelector('.cke_wysiwyg_frame');
                    if(editor_iframe){
                        let iframe_doc=editor_iframe.contentWindow.document;
                        if(iframe_doc){
                            clearInterval(interval);
                            task(iframe_doc); }}}}
            else{
                end_target(); }} // work()


        function task(doc){ // taskは開いた編集画面での作業コード 🟦⬜🟦
            Promise.all([
                task_in1(),
                task_in2(doc),
                strage_write(),
                scan_disp(),
                hit_display() ])
                .then(end_target()) }


        function task_in1(){ // 記事タイトルの検索 🟦⬜🟦
            let title=new_win[k].document.querySelector('.p-title__text').value;
            if(search_word1e!="" && title){
                let regex1=new RegExp(search_word1e);
                let result1=regex1.test(title); // 検索❶：結果「hit1」

                let index=entry_id_DB.indexOf(entry_id[k].value);
                if(index==-1){ // IDがblogDBに記録されていない場合
                    if(result1==true){
                        blogDB.push([entry_id[k].value, 1, 0]); }} // 記事ID/フラグを追加
                else{ // IDがblogDBに記録されていた場合
                    if(result1==true){
                        blogDB[index][1]=1; } // 記事ID/フラグ「1」を更新
                    else{
                        blogDB[index][1]=0; }} // 記事ID/フラグ「0」を更新
                reg_set(); }}


        function task_in2(doc){ // ブログ本文の検索 🟦⬜🟦
            let iframe_body=doc.querySelector('.cke_editable');
            let cke_text=iframe_body.textContent;
            if(search_word2e!="" && cke_text){
                let regex2=new RegExp(search_word2e);
                let result2=regex2.test(cke_text); // 検索❷：結果「hit2」

                let index=entry_id_DB.indexOf(entry_id[k].value);
                if(index==-1){ // IDがblogDBに記録されていない場合
                    if(result2==true){
                        blogDB.push([entry_id[k].value, 0, 1]); }} // 記事ID/フラグを追加
                else{ // IDがblogDBに記録されていた場合
                    if(result2==true){
                        blogDB[index][2]=1; } // 記事ID/フラグ「1」を更新
                    else{
                        blogDB[index][2]=0; }} // 記事ID/フラグ「0」を更新
                reg_set(); }}


        function strage_write(){
            let write_json=JSON.stringify(blogDB);
            localStorage.setItem('EPW_DB_back', write_json); }// ストレージ保存


        function end_target(){ // 終了処理
            let editor_flg=new_win[k].document.querySelector('input[name="editor_flg"]');
            list_bar[k].style.boxShadow='none';
            if(editor_flg.value=='5'){
                list_bar[k].style.background='#caedf2'; } // 処理済
            else{
                list_bar[k].style.background='#eceff1'; } // 標準エディタ外の文書は未処理

            new_win[k].close();
            setTimeout(()=>{
                next_do(k); }, 10); //⏩

            function next_do(k){
                next_target=k+1;
                if(next_target<entry_target.length){ open_win(next_target); }
                else{ next_call(); }}} // ページの終りまで終了した状態

    } // open_win()


    function next_call(){
        let win_url;
        let current;
        let pageid;
        let next_url;
        let pager;
        let end;

        blogDB[0][1]='c'; // 連続動作フラグを連続にセット
        let write_json=JSON.stringify(blogDB);
        localStorage.setItem('EPW_DB_back', write_json); // ローカルストレージ保存

        win_url=window.location.search.substring(1,window.location.search.length);
        current=win_url.slice(-6);

        if(win_url.indexOf('pageID') ==-1){ // pageIDが無い 月のトップページの場合
            pager=document.querySelector('.pagingArea');
            if(pager){ // ページャーが有りその末尾でなければ同月次ページへ
                next_url=
                    'https://blog.ameba.jp/ucs/entry/srventrylist.do?pageID=2&entry_ym='+current;
                window.open( next_url, '_self'); }
            else{ // ページャーが無ければ次月トップページへ
                current=make_next(current);
                if(current!=0){ // 現在を越えないなら次月へ
                    next_url=
                        'https://blog.ameba.jp/ucs/entry/srventrylist.do?entry_ym='+current;
                    window.open( next_url, '_self'); }
                else{ // 現在を越えたら0が戻り停止
                    setTimeout(()=>{
                        when_edge();
                    }, 1000); }}}

        else{ // pageIDを含み 月のトップページでない場合
            end=document.querySelector('.pagingArea .disabled.next');
            if(!end){ // ページャーの末尾でなければ同月次ページへ
                pageid=parseInt(win_url.slice(7).slice(0, -16), 10) +1;
                next_url=
                    'https://blog.ameba.jp/ucs/entry/srventrylist.do?pageID='+
                    pageid+'&entry_ym='+current;
                window.open( next_url, '_self'); }
            else{ // ページャーの末尾なら次月トップページへ
                current=make_next(current);
                if(current!=0){ // 現在を越えないなら次月へ
                    next_url='https://blog.ameba.jp/ucs/entry/srventrylist.do?entry_ym='+current;
                    window.open( next_url, '_self'); }
                else{ // 現在を越えたら0が戻り停止
                    setTimeout(()=>{
                        when_edge();
                    }, 1000); }}}

        function make_next(curr){
            let ym;
            let y;
            let m;
            ym=parseInt(curr, 10); // 10進数値化
            y=Math.floor(ym/100); // 年は100で割った商
            m=ym % 100; // 月は100で割った余り
            if(m !=12){
                ym=100*y + m +1; }
            else{
                ym=100*y + 101; }

            let now=new Date();
            if(ym > 100*now.getFullYear() + now.getMonth() +1){
                return 0; } // 現在の月を越える場合は0を返す
            else{
                return ym; }} // 次年月の数値を返す

        function when_edge(){
            blogDB[0][1]='s'; // 連続動作フラグをリセット
            let write_json=JSON.stringify(blogDB);
            localStorage.setItem('EPW_DB_back', write_json); // ローカルストレージ保存
            document.querySelector('#div0').remove();
            document.querySelector('#div1').remove();
            control_pannel('e'); } // 全作業の終了時の表示をさせる

    } // next_call()


    // 編集済みの記事にマークを付ける
    let ed_sw=document.querySelectorAll('.actions .action:first-child');
    for(let k=0; k<ed_sw.length; k++){
        ed_sw[k].onmousedown=()=>{
            ed_sw[k].style.boxShadow='inset #2196f3 -16px 0 0 -10px'; }}

    let ed_link=document.querySelectorAll('.actions a');
    for(let k=0; k<ed_link.length; k++){
        ed_link[k].setAttribute('target', '_blank'); }

}); // 親ウインドウで動作
