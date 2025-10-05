<!--
Rabbit R1 Camera Access

Camera Access
Access the device camera using standard web APIs like navigator.mediaDevices.getUserMedia. The R1 supports both forward-facing ('environment') and user-facing ('user') cameras. Photos can be captured via canvas drawing and stored in local storage (or using CreationStorageHandler for consistency).

Notes:
- Ensure the video element fits within the 240x282px screen.
- Use hardware buttons like PTT (sideClick) for capture and scroll wheel for switching camera direction.
- Storage uses base64-encoded images for persistence.
- Implement flash effects via CSS overlays for visual feedback.
-->

<script>
let cameraStream;
let currentFacingMode = 'environment';
let isSwitching = false;
const videoElement = document.querySelector('video');
const photoKeyPrefix = 'r1_photo_';
let currentDeleteIndex = -1;

async function startCamera() {
  try {
    cameraStream = await navigator.mediaDevices.getUserMedia({
      video: { facingMode: currentFacingMode }
    });
    videoElement.srcObject = cameraStream;
    videoElement.play();
    log(`Camera preview started: ${currentFacingMode}`);
  } catch (e) {
    log(`Camera access error: ${e.message}`);
  }
}

function stopCamera() {
  if (cameraStream) {
    cameraStream.getTracks().forEach(track => track.stop());
    videoElement.srcObject = null;
    log('Camera stopped');
  }
}

async function switchDirection() {
  if (!cameraStream || isSwitching) return;
  isSwitching = true;
  stopCamera();
  currentFacingMode = currentFacingMode === 'environment' ? 'user' : 'environment';
  await startCamera();
  log(`Switched to ${currentFacingMode}`);
  setTimeout(() => { isSwitching = false; }, 1000);
}

async function captureAndSavePhoto() {
  if (!cameraStream) return log('Start camera first');
  try {
    const canvas = document.createElement('canvas');
    canvas.width = 240;
    canvas.height = 282;
    const ctx = canvas.getContext('2d');
    ctx.drawImage(videoElement, 0, 0, 240, 282);
    const base64Image = canvas.toDataURL('image/jpeg');

    let photoCount = localStorage.getItem('photo_count') ? parseInt(localStorage.getItem('photo_count'), 10) : 0;
    const key = photoKeyPrefix + photoCount;
    localStorage.setItem(key, base64Image.split(',')[1]);
    photoCount++;
    localStorage.setItem('photo_count', photoCount.toString());
    log(`Photo captured and saved as ${key}: ${base64Image.substr(0, 20)}...`);
  } catch (e) {
    log(`Capture/save error: ${e.message}`);
  }
}

async function viewImages() {
  const gallery = document.getElementById('gallery');
  gallery.innerHTML = '';
  try {
    let localCount = localStorage.getItem('photo_count') ? parseInt(localStorage.getItem('photo_count'), 10) : 0;
    for (let i = 1; i <= localCount; i++) {
      const key = photoKeyPrefix + (i - 1);
      const base64 = localStorage.getItem(key);
      if (base64) {
        const img = document.createElement('img');
        img.src = 'data:image/jpeg;base64,' + base64;
        img.dataset.index = i - 1;
        gallery.appendChild(img);
      }
    }
    log(`Loaded ${localCount} photos`);
  } catch (e) {
    log(`Error loading photos: ${e.message}`);
  }
}

async function deleteCurrentPhoto() {
  if (currentDeleteIndex === -1) return;
  try {
    const key = photoKeyPrefix + currentDeleteIndex;
    localStorage.removeItem(key);

    let photoCount = parseInt(localStorage.getItem('photo_count'), 10);
    for (let j = currentDeleteIndex + 1; j < photoCount; j++) {
      const oldKey = photoKeyPrefix + j;
      const newKey = photoKeyPrefix + (j - 1);
      const data = localStorage.getItem(oldKey);
      if (data) {
        localStorage.setItem(newKey, data);
      }
      localStorage.removeItem(oldKey);
    }

    photoCount--;
    localStorage.setItem('photo_count', photoCount.toString());
    log(`Deleted photo at index ${currentDeleteIndex}`);
    document.getElementById('fullscreen').style.display = 'none';
    viewImages();
  } catch (e) {
    log(`Error deleting photo: ${e.message}`);
  }
}

function flash() {
  const flash = document.getElementById('flash-overlay');
  flash.classList.add('flash');
  setTimeout(() => {
    flash.classList.remove('flash');
  }, 300);
}

window.addEventListener('sideClick', () => {
  captureAndSavePhoto();
});

window.addEventListener('scrollUp', switchDirection);
window.addEventListener('scrollDown', switchDirection);
</script>

<style>
#flash-overlay {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: white;
  opacity: 0;
  pointer-events: none;
  transition: opacity 0.3s ease;
}

.flash {
  opacity: 0.8;
}
</style>
