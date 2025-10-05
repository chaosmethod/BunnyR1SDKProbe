
Rabbit R1 Camera Access


 Camera Access
Access the device camera using standard web APIs like navigator.mediaDevices.getUserMedia. The r1 supports both forward-facing ('environment') and user-facing ('user') cameras. Photos can be captured via canvas drawing and stored in local storage (or using CreationStorageHandler for consistency).
Notes:

Ensure the video element fits within the 240x282px screen.
Use hardware buttons like PTT (sideClick) for capture and scroll wheel for switching camera direction.
Storage uses base64-encoded images for persistence.
Implement flash effects via CSS overlays for visual feedback.

Starting the Camera
javascriptasync function startCamera() {
  try {
    cameraStream = await navigator.mediaDevices.getUserMedia({
      video: { facingMode: currentFacingMode } // 'environment' or 'user'
    });
    videoElement.srcObject = cameraStream;
    videoElement.play();
    log('Camera preview started: ${currentFacingMode}');
  } catch (e) {
    log('Camera access error: ${e.message}');
  }
}
Stopping the Camera
javascriptfunction stopCamera() {
  if (cameraStream) {
    cameraStream.getTracks().forEach(track => track.stop());
    videoElement.srcObject = null;
    log('Camera stopped');
  }
}
Switching Camera Direction
javascriptasync function switchDirection() {
  if (!cameraStream || isSwitching) return;
  isSwitching = true;
  stopCamera(); // Stop current stream
  currentFacingMode = currentFacingMode === 'environment' ? 'user' : 'environment';
  await startCamera(); // Restart with new mode
  log('Switched to ${currentFacingMode}');
  setTimeout(() => { isSwitching = false; }, 1000); // Pause for 1 second
}
Capturing and Saving a Photo
javascriptasync function captureAndSavePhoto() {
  if (!cameraStream) return log('Start camera first');
  try {
    const canvas = document.createElement('canvas');
    canvas.width = 240;
    canvas.height = 282;
    const ctx = canvas.getContext('2d');
    ctx.drawImage(videoElement, 0, 0, 240, 282);
    const base64Image = canvas.toDataURL('image/jpeg');

    // Save to storage
    const photoKeyPrefix = 'r1_photo_';
    let photoCount = await localStorage.getItem('photo_count') ? parseInt(localStorage.getItem('photo_count'), 10) : 0;
    const key = photoKeyPrefix + photoCount;
    localStorage.setItem(key, base64Image.split(',')[1]); // Store base64 without prefix
    photoCount++;
    localStorage.setItem('photo_count', photoCount.toString());
    log(`Photo captured and saved as ${key}: [base64Image.substr(0, 20)]...`);
  } catch (e) {
    log(`Capture/save error: ${e.message}`);
  }
}
Viewing Stored Images (Gallery)
javascriptasync function viewImages() {
  const gallery = document.getElementById('gallery');
  gallery.innerHTML = ''; // Clear previous
  try {
    let localCount = localStorage.getItem('photo_count') ? parseInt(localStorage.getItem('photo_count'), 10) : 0;
    for (let i = 1; i <= localCount; i++) { // Assuming 1-indexed
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
Deleting a Photo
javascriptasync function deleteCurrentPhoto() {
  if (currentDeleteIndex === -1) return;
  try {
    const key = photoKeyPrefix + currentDeleteIndex;
    localStorage.removeItem(key);

    // Shift remaining photos
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
    log('Deleted photo at index ${currentDeleteIndex}');
    document.getElementById('fullscreen').style.display = 'none'; // Hide fullscreen view
    viewImages(); // Reload gallery
  } catch (e) {
    log(`Error deleting photo: ${e.message}`);
  }
}
Flash Effect
Use a CSS overlay for a flash simulation when capturing photos.
css#flash-overlay {
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
javascriptfunction flash() {
  const flash = document.getElementById('flash-overlay');
  flash.classList.add('flash');
  setTimeout(() => {
    flash.classList.remove('flash');
  }, 300);
}
Integrating with Hardware Events
javascript// Side button for capture
window.addEventListener('sideClick', () => {
  captureAndSavePhoto();
});

// Scroll wheel for switch
window.addEventListener('scrollUp', switchDirection);
window.addEventListener('scrollDown', switchDirection);