Un petit code fait maison pour inverser la lecture et limiter la vitesse afin d’entendre le flag :
```
<!DOCTYPE html>
<html>
<body>
    <input type='file' id="file">
    <input type="number" id="speed" value="0.3" min="0.1" max="0.7" step="0.05">
    <button onclick='openFile(event)'>Ecouter</button>
<script>
const audioCtx = new AudioContext();
const reader = new FileReader();    
function openFile(event) {
    if (document.getElementById("file").files[0]) {
        reader.readAsArrayBuffer(document.getElementById("file").files[0]);
            reader.onload = function(e) {
            audioCtx.decodeAudioData(reader.result, (buffer) => {
                const audio = audioCtx.createBufferSource();
                Array.prototype.reverse.call(buffer.getChannelData(0));
                Array.prototype.reverse.call(buffer.getChannelData(1));
                audio.buffer = buffer;
                audio.connect(audioCtx.destination);
                audio.playbackRate.value = document.getElementById("speed").value;
                audio.start(0);
            });
        };
    }
}
</script>
</body>
</html>
```
