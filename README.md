const CACHE_NAME = "faceapi-cache-v1";
const MODEL_PATH = "/TSUISLARS/faceApi/"; // adjust if your path is different

// List all required model files with correct extensions
const FILES_TO_CACHE = [
    MODEL_PATH + "tiny_face_detector_model-weights_manifest.json",
    MODEL_PATH + "tiny_face_detector_model-shard1.bin",
    MODEL_PATH + "face_landmark_68_tiny_model-weights_manifest.json",
    MODEL_PATH + "face_landmark_68_tiny_model-shard1.bin",
    MODEL_PATH + "face_recognition_model-weights_manifest.json",
    MODEL_PATH + "face_recognition_model-shard1.bin"
];

// Install event → cache model files
self.addEventListener("install", (event) => {
    event.waitUntil(
        caches.open(CACHE_NAME).then((cache) => {
            return cache.addAll(FILES_TO_CACHE);
        })
    );
    self.skipWaiting();
});

// Activate event → cleanup old caches
self.addEventListener("activate", (event) => {
    event.waitUntil(
        caches.keys().then((keyList) =>
            Promise.all(
                keyList.map((key) => {
                    if (key !== CACHE_NAME) {
                        return caches.delete(key);
                    }
                })
            )
        )
    );
    self.clients.claim();
});

// Fetch event → serve models from cache first
self.addEventListener("fetch", (event) => {
    if (event.request.url.includes(MODEL_PATH)) {
        event.respondWith(
            caches.match(event.request).then((response) => {
                return response || fetch(event.request);
            })
        );
    }
});




i have this sw.js
const CACHE_NAME = "faceapi-cache-v1";
const MODEL_PATH = "/TSUISLARS/faceApi/"; // adjust if your path is different

// List the models you want to cache permanently
const FILES_TO_CACHE = [
    MODEL_PATH + "tiny_face_detector_model-weights_manifest.json",
    MODEL_PATH + "tiny_face_detector_model-shard1",
    MODEL_PATH + "face_landmark_68_tiny_model-weights_manifest.json",
    MODEL_PATH + "face_landmark_68_tiny_model-shard1",
    MODEL_PATH + "face_recognition_model-weights_manifest.json",
    MODEL_PATH + "face_recognition_model-shard1"
];

// Install event → cache all model files
self.addEventListener("install", (event) => {
    event.waitUntil(
        caches.open(CACHE_NAME).then((cache) => {
            return cache.addAll(FILES_TO_CACHE);
        })
    );
    self.skipWaiting();
});

// Activate event → cleanup old caches if version changes
self.addEventListener("activate", (event) => {
    event.waitUntil(
        caches.keys().then((keyList) => {
            return Promise.all(
                keyList.map((key) => {
                    if (key !== CACHE_NAME) {
                        return caches.delete(key);
                    }
                })
            );
        })
    );
    self.clients.claim();
});

// Fetch event → serve models from cache
self.addEventListener("fetch", (event) => {
    if (FILES_TO_CACHE.some((file) => event.request.url.includes(file))) {
        event.respondWith(
            caches.match(event.request).then((response) => {
                return response || fetch(event.request);
            })
        );
    }
});
