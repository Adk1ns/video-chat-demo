<script>
  import {Peer} from 'peerjs'
let peer = new Peer();
let codeid = ""
let videocurrent;
let videoEl;
let youid = ""

  // get your ID
  peer.on("open",(id)=>{
    youid = id
    console.log(id)
  })
  // error on ID
   peer.on("error",(id)=>{
    console.log("error id "+ id)
  })

  peer.on("connection",(conn)=>{
    console.log("message....")
    conn.on("data",(data)=>{
      console.log("new data " + data)
    })
    conn.on("open",()=>{
      console.log("new message")
    })
  })

  // handle connection
  peer.on("call",async(call)=>{
    // open webcam
  await navigator.mediaDevices.getUserMedia({
    video:true,
    audio:true
  }).then((stream)=>{
    call.answer(stream)
    call.on("stream",renderYouwebcam)
    videocurrent.srcObject = stream
    videocurrent.play()
  }).catch(err=>console.log("err msg" + err))
})
// render your webcam
let renderYouwebcam = (stream)=>{
  console.log(stream)
  videoEl.srcObject = stream
  videoEl.play()
}

</script>
<div>
  you id cam  = {youid}
  <br>
  code : <input type=""
  bind:value={codeid} name="">

  <button
  on:click={async()=>{
    var conn = peer.connect(codeid)
    conn.on("data",(data)=>{
      console.log("new data " + data)
    })
    conn.on("open",function(){
      conn.send("hi")
    })
    // OPEN YOU WEBAM
    await navigator.mediaDevices.getUserMedia({
      video:true,
      audio:true
    }).then(stream=>{
      let call = peer.call(codeid,stream)
      videocurrent.srcObject = stream
      videocurrent.play()
      call.on("stream",renderYouwebcam)
    }).catch(err=>console.log("have error " + err))
  }}
  >
  connect</button>

  <h1>PiperChat</h1>
  <p>No user consent form required!</p>
  <!--FRIEND -->
  <div>
    <p>friend</p>
  <video 
  bind:this={videoEl}
  width="400" height="400" autoplay={true}>
    <track kind="captions" src="">
  </video>
  </div>
  <br>

  <!-- YOU -->
  <div>
    <p>me</p>
  <video 
  bind:this={videocurrent}
  width="400" height="400" autoplay={true}>
    <track kind="captions" src="">
  </video>
  </div>
</div>