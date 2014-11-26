anthonymcg22.github.io
======================

Make your own flashcard questions to study 

Down below is new code for javascript file

"use strict";
var flashview = {};   //make object clothing
flashview.QandA = [];   //array of question objects
flashview.shortQ = [];

flashview.addQ = function () {   // onclick function of button
    var body = document.getElementById("body");    //grabs body of "notecard" and makes inputs to add question
    body.innerHTML = '<input id="name" type="text" placeholder="Enter your name"><br/><input id="Q" type="text" placeholder="Enter question"><br/><textarea id="A" placeholder="Enter answer"></textarea><br/><div id="2Btns"><span class="btn btn-success" onclick="flashview.submitQ()">Submit</span><span onclick="flashview.drawHome()" class="btn btn-danger">Cancel</span></div>';
};
flashview.submitQ = function () {       //adds an object with props: name, Q, A  to array
    var name = document.getElementById("name");    //three input fields
    var Q = document.getElementById("Q");
    var A = document.getElementById("A");
    //if any field is empty alert
    if (name.value === "" || Q.value === "" || A.value === "") { alert("All fields must contain a value"); }
    else {
        var newQ = { name: name.value, Q: Q.value, A: A.value };  //make object with 3 properties holding input values
        flashview.AJAX('POST', "https://flashview.firebaseio.com/.json", function (data) { flashview.getKey(data, newQ) }, newQ);
    };
};
flashview.drawHome = function () {        //shows home page with table of questions
    var body = document.getElementById("body");  //grab body element
    document.getElementById("left").innerHTML = '<div onclick="flashview.addQ()" class="btn btn-success"> ADD QUESTION </div>';
    var table = '<table class="table"><thead><th>Name</th><th>Question</th><th><button style="font-size: 1.6em;" class="btn btn-info" onclick="flashview.search()">Search</button></th><th><input type="text" id="search" placeholder="search criteria"></th></thead><tbody id="tbody">'; //start a table
    body.innerHTML = "";    //clear body
    for (var i = 0; i < flashview.QandA.length; i++) {    //go through array and make table rows and cells of question objects
        table += '<tr class="m"><td><span><strong>' + flashview.QandA[i].name + '</strong></span></td><td><span onclick="flashview.run(' + i + ')">' + flashview.shortQ[i] + '</span></td><td><button onclick="flashview.edit(' + i + ')" class="btn btn-danger">Edit</button></td><td><button onclick="flashview.delete(' + i + ',\'' + flashview.QandA[i].key + '\')" class="btn btn-warning">Delete</button></td></tr>';
    };
    table += '</tbody></table>';  //closing tags for table
    if (flashview.QandA.length === 0)
        body.innerHTML = '<table class="table"><thead><th>Name</th><th>Question</th><th><button style="font-size: 1.6em;" class="btn btn-info" onclick="flashview.search()">Search</button></th><th><input type="text" id="search" placeholder="search criteria"></th></thead><tbody id="tbody"></tbody></table>';
    else
        body.innerHTML += table;   //add table to body
};

flashview.sort = function () {
    flashview.QandA.sort(function (a, b) {
        if (a["Q"].toLowerCase() < b["Q"].toLowerCase()) {
            return -1;
        } else if (a["Q"].toLowerCase() > b["Q"].toLowerCase()) {
            return 1;
        } else {
            return 0;
        }
    });
    flashview.shortQ = [];
    for (var i = 0; i < flashview.QandA.length; i++) {
        if (flashview.QandA[i].Q.length > 45)
            flashview.shortQ.push(flashview.QandA[i].Q.slice(0, 42) + "...");
        else { flashview.shortQ.push(flashview.QandA[i].Q); }
    };
    flashview.drawHome();
};
flashview.run = function (index) {   // called when question clicked on, takes array index as a paramemeter
    var body = document.getElementById("body");  // grabs body element    
    //adding Home button to left div, replacing add question button
    document.getElementById("left").innerHTML = '<div onclick="flashview.drawHome()" class="btn btn-success"> HOME </div>';
    // set onclick to whole card so it will have a function that flips between answer and question
    if (body.innerHTML === '<div class="col-md-2" style="height: 320px;"><button id="goLeft" class="btn" style="margin-top: 140px;" onclick="flashview.left(' + index + ')">⇦</button></div><div id="middle" onclick="flashview.run(' + index + ')" class="col-md-8" style="position: relative; height: 340px;"><br><h3 id="Q">' + flashview.QandA[index].Q + '</h3><p id="A" style="visibility: hidden;">' + flashview.QandA[index].A + '</p><p style="font-size: 11px; position: absolute; bottom: 0; left: 40%;">Created by ' + flashview.QandA[index].name + '</p></div><div class="col-md-2" style="height: 320px;"><button id="goRight" class="btn" style="margin-top: 140px;" onclick="flashview.right(' + index + ')">⇨</button></div>')
        // if #body has the <h2> question and invisible <p> answer and two buttons, switch the invisibility of the <h2> and <p>
    {
        body.innerHTML = '<div class="col-md-2" style="height: 320px;"><button id="goLeft" class="btn" style="margin-top: 140px;" onclick="flashview.left(' + index + ')">⇦</button></div><div id="middle" onclick="flashview.run(' + index + ')" class="col-md-8" style="position: relative; height: 340px;"><br><h3 id="Q" style="visibility: hidden;">' + flashview.QandA[index].Q + '</h3><p id="A">' + flashview.QandA[index].A + '</p><p style="font-size: 11px; position: absolute; bottom: 0; left: 40%;">Created by ' + flashview.QandA[index].name + '</p></div><div class="col-md-2" style="height: 320px;"><button id="goRight" class="btn" style="margin-top: 140px;" onclick="flashview.right(' + index + ')">⇨</button></div>';
    }
        //else make #body have <h2> question and invisible <p> answer and two buttons,  clicking on card switches between Q and A
    else { body.innerHTML = '<div class="col-md-2" style="height: 320px;"><button id="goLeft" class="btn" style="margin-top: 140px;" onclick="flashview.left(' + index + ')">⇦</button></div><div id="middle" onclick="flashview.run(' + index + ')" class="col-md-8" style="position: relative; height: 340px;"><br><h3 id="Q">' + flashview.QandA[index].Q + '</h3><p id="A" style="visibility: hidden;">' + flashview.QandA[index].A + '</p><p style="font-size: 11px; position: absolute; bottom: 0; left: 40%;">Created by ' + flashview.QandA[index].name + '</p></div><div class="col-md-2" style="height: 320px;"><button id="goRight" class="btn" style="margin-top: 140px;" onclick="flashview.right(' + index + ')">⇨</button></div>'; }
};
flashview.left = function (i) {
    if (i === 0) {
        i = flashview.QandA.length; }
    flashview.run(i - 1);
};
flashview.right = function (i) {
    if (i === flashview.QandA.length - 1) {
        i = -1;  }
    flashview.run(i + 1);
};
flashview.edit = function (i) {
    var tbody = document.getElementById("tbody");  //grabbing table body
    //changing the corresponding row to be
    tbody.childNodes[i].innerHTML = '<tr><td><input id="1" type="text" value="' + flashview.QandA[i].name + '"></td><td><input id="2" type="text" value="' + flashview.QandA[i].Q + '"></td><td><input id="3" type="text" value="' + flashview.QandA[i].A + '"></td><td><span class="btn btn-danger" onclick="flashview.saveEdit(' + i + ',\'' + flashview.QandA[i].key + '\')">Save Edit</span></td><td><span onclick="flashview.cancelEdit()" class="btn btn-success">Cancel Edit</span></td></tr>';
    for (var h = 0; h < flashview.QandA.length; h++) {
        if (h !== i) {
            document.getElementById("tbody").childNodes[h].getElementsByTagName("button")[0].removeAttribute("onclick");
            document.getElementById("tbody").childNodes[h].getElementsByTagName("button")[1].removeAttribute("onclick");
        };
    };
    document.getElementById("left").getElementsByTagName("div")[0].removeAttribute("onclick");
};
flashview.saveEdit = function(i, key) {

    var name = document.getElementById("1").value;
    var Q = document.getElementById("2").value;
    var A = document.getElementById("3").value;
    flashview.QandA.splice(i, 1);
    flashview.QandA.splice(i, 0, { name: name, Q: Q, A: A, key: key });
    flashview.AJAX('PUT', "https://flashview.firebaseio.com/" + key + ".json", function () { flashview.sort(); }, { name: name, Q: Q, A: A });
};
flashview.cancelEdit = function () {
    flashview.drawHome();
};
flashview.delete = function (i, key) {
    flashview.QandA.splice(i, 1);
    flashview.AJAX('DELETE', "https://flashview.firebaseio.com/" + key + ".json", function () { flashview.sort(); });
};
flashview.AJAX = function (method, url, callback, data, criteria) {
    var request = new XMLHttpRequest();
    request.open(method, url, true);
    request.onload = function () {
        if (this.status >= 200 & this.status < 400) {
            if (callback) {
                callback(JSON.parse(this.response), criteria);
            }
        }
        else {
            alert(this.response);
        }
    };
    request.onerror = function () {
        alert("Big Huge Error!!!");
    };
    if (data)
        request.send(JSON.stringify(data));
    else
        request.send();
};
flashview.getKey = function (data, item) {
    item.key = data.name;
    flashview.QandA.push(item);
    flashview.sort();
};
flashview.initialGet = function (data) {
    for (var x in data) {
        data[x].key = x;
        flashview.QandA.push(data[x]);
    };
    flashview.sort();
};
flashview.returnSearchResults = function (data, criteria) {
    flashview.QandA = [];
    for (var x in data) {
        data[x].key = x;

        if (data[x].name.indexOf(criteria) === -1)
            flashview.QandA = [];
        if (criteria === "")
        flashview.QandA.push(data[x]);
        else if (data[x].name.indexOf(criteria) > -1)
            flashview.QandA.push(data[x]);
        
        alert(data[x].name.indexOf(criteria));
    };
        alert(criteria);
    flashview.sort();
};
flashview.searchCriteria = document.getElementById("search");
flashview.search = function() {
    flashview.AJAX('GET', "https://flashview.firebaseio.com/.json", flashview.returnSearchResults, null, flashview.searchCriteria.value);
};
    flashview.AJAX('GET', "https://flashview.firebaseio.com/.json", flashview.initialGet);


