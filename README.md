DOCTYPE html>
<html lang="hi">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Advanced Smart Dashboard</title>

<style>
body{
    font-family: Arial;
    margin:0;
    background:#f3f4f6;
}

/* Header */
.header{
    background:linear-gradient(135deg,#6c5ce7,#0984e3);
    color:white;
    text-align:center;
    padding:25px;
    border-radius:0 0 25px 25px;
}

/* Card */
.card{
    background:white;
    margin:15px;
    padding:15px;
    border-radius:15px;
    box-shadow:0 5px 10px rgba(0,0,0,0.1);
}

input,select{
    width:100%;
    padding:10px;
    margin-top:10px;
    border-radius:10px;
    border:1px solid #ccc;
}

button{
    padding:10px;
    border:none;
    border-radius:10px;
    color:white;
    cursor:pointer;
}

.green{background:#00b894;}
.red{background:#d63031;}
.blue{background:#0984e3;}

.task{
    background:#f1f1f1;
    padding:10px;
    margin-top:10px;
    border-radius:10px;
}

.done{
    text-decoration: line-through;
    color: gray;
}

/* Progress */
.progress{
    height:10px;
    background:#ddd;
    border-radius:10px;
    overflow:hidden;
}
.progress-bar{
    height:100%;
    background:#00b894;
}
</style>
</head>

<body>

<div class="header">
    <h1>Smart Study Dashboard 🚀</h1>
    <h2 id="time"></h2>
</div>

<div class="card">
    <h3>➕ Add Task</h3>

    <input id="task" placeholder="Task likho">

    <select id="category">
        <option>Study</option>
        <option>Coding</option>
        <option>Exercise</option>
    </select>

    <input type="time" id="reminder">

    <button class="green" onclick="addTask()">Add Task</button>
</div>

<div class="card">
    <h3>📊 Progress</h3>
    <div class="progress">
        <div id="progressBar" class="progress-bar"></div>
    </div>
    <p id="progressText"></p>
</div>

<div class="card">
    <h3>📋 Tasks</h3>
    <div id="taskList"></div>
</div>

<div class="card">
    <h3>📜 History</h3>
    <div id="history"></div>
</div>

<script>

/* Time */
setInterval(()=>{
    time.innerText=new Date().toLocaleTimeString();
},1000);

/* Notification Permission */
Notification.requestPermission();

/* Add Task */
function addTask(){
    let task=document.getElementById("task").value;
    let category=document.getElementById("category").value;
    let reminder=document.getElementById("reminder").value;

    if(task=="") return alert("Task likho");

    let data=JSON.parse(localStorage.getItem("tasks"))||[];

    data.push({
        task,
        category,
        reminder,
        done:false,
        date:new Date().toLocaleString()
    });

    localStorage.setItem("tasks",JSON.stringify(data));

    document.getElementById("task").value="";

    loadTasks();
}

/* Load Tasks */
function loadTasks(){
    let data=JSON.parse(localStorage.getItem("tasks"))||[];
    let html="";

    let done=0;

    data.forEach((item,index)=>{
        if(item.done) done++;

        html+=`
        <div class="task ${item.done?"done":""}">
            <b>${item.task}</b> (${item.category})<br>
            ⏰ ${item.reminder || "No time"}
            <br>
            <button class="blue" onclick="toggle(${index})">Done</button>
            <button class="red" onclick="deleteTask(${index})">Delete</button>
        </div>`;
    });

    taskList.innerHTML=html;

    /* Progress */
    let percent=data.length? (done/data.length)*100:0;
    progressBar.style.width=percent+"%";
    progressText.innerText=`${done}/${data.length} Completed`;

    /* History */
    loadHistory(data);
}

/* Toggle Done */
function toggle(i){
    let data=JSON.parse(localStorage.getItem("tasks"));
    data[i].done=!data[i].done;
    localStorage.setItem("tasks",JSON.stringify(data));
    loadTasks();
}

/* Delete */
function deleteTask(i){
    let data=JSON.parse(localStorage.getItem("tasks"));
    data.splice(i,1);
    localStorage.setItem("tasks",JSON.stringify(data));
    loadTasks();
}

/* History */
function loadHistory(data){
    let html="";
    data.slice().reverse().forEach(item=>{
        html+=`
        <div class="task">
        ${item.task} ✔ ${item.done?"Done":"Pending"}<br>
        ${item.date}
        </div>`;
    });
    history.innerHTML=html;
}

/* Reminder System */
setInterval(()=>{
    let data=JSON.parse(localStorage.getItem("tasks"))||[];
    let now=new Date().toTimeString().slice(0,5);

    data.forEach(item=>{
        if(item.reminder==now && !item.done){
            new Notification("Reminder: "+item.task);
        }
    });

},60000);

/* Init */
loadTasks();

</script>

</body>
</html>
