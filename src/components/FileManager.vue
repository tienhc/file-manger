<template>
    <div class="file-manager"
        style="display: grid; grid-template-columns: 25% 75%; gap: 20px; height: 100vh; padding: 20px;">
        <!-- Sidebar -->
        <div class="sidebar"
            style="background: #ffffff; padding: 20px; box-shadow: 0 0 10px rgba(0, 0, 0, 0.1); border-radius: 10px;">
            <button onclick="importDocuments()"
                style="width: 100%; background-color: #3b82f6; color: white; padding: 10px; border: none; border-radius: 10px; cursor: pointer; margin-bottom: 20px;">
                Import documents
            </button>

            <div>
                <p style="font-weight: bold; margin-bottom: 10px;">Storage <span
                        style="float: right; color: #3b82f6; cursor: pointer;">Change plan</span></p>
                        <div style="background: #e5e7eb; height: 8px; border-radius: 5px; margin-bottom: 10px;">
                            <div :style="{ width: percentUsed + '%', height: '100%', background: '#3b82f6', borderRadius: '5px' }"></div>
                          </div>
                          <p style="margin-bottom: 20px;">{{ percentUsed }}% used of 2GB</p>

                <input type="text" placeholder="e.g. image.png" v-model="searchText" 
                    style="width: 100%; padding: 8px; border: 1px solid #e5e7eb; border-radius: 5px; margin-bottom: 20px;" />

                <p style="font-weight: bold;">Folders</p>
                <ul>
                    <li v-for="folder in parentFolders" :key="folder.id">
                        <div @click="toggleDropdown(folder.id)" style="cursor: pointer;">
                            <span>{{ folderDropdowns[folder.id] ? '▼' : '▶' }}</span>
                            {{ folder.name }}
                        </div>
                        <ul v-if="folderDropdowns[folder.id]" style="margin-left: 20px;">
                            <li v-for="child in getChildFolders(folder.id)" :key="child.id">
                                <input type="radio" name="folder" 
                                :value="child.id" 
                                :checked="folderIdClicked === child.id" 
                                @change="folderIdClicked = child.id" /> 
                                {{ child.name }}
                            </li>
                        </ul>
                    </li>
                </ul>

                <p style="font-weight: bold; margin-top: 20px;">Members</p>
                <input type="checkbox" id="selectAll" /> Select All<br>
                <input type="checkbox" id="admin" checked /> Admin<br>
                <input type="checkbox" id="user" /> User
            </div>
        </div>

        <!-- Content -->
        <div class="content"
            style="background: #ffffff; padding: 20px; box-shadow: 0 0 10px rgba(0, 0, 0, 0.1); border-radius: 10px;">
            <input type="text" placeholder="Search Input" v-model="searchFileName"
                style="width: 100%; padding: 8px; border: 1px solid #e5e7eb; border-radius: 5px; margin-bottom: 20px;" />
            <table style="width: 100%; border-collapse: collapse;">
                <thead>
                    <tr>
                        <th style="text-align: left;"><input type="checkbox" @change="selectAll = ! selectAll"  /> Select all</th>
                        <td></td>
                        <th style="text-align: left; padding: 10px;">Name</th>
                        <th style="text-align: left; padding: 10px;">Dimension</th>
                        <th style="text-align: left; padding: 10px;">Size</th>
                    </tr>
                </thead>
                <tbody id="imageTableBody">
                    <tr v-for="data in dataImageSearch" :key="data.id">
                        <td><input type="checkbox" :value="data.id" :checked="selectAll == true" /></td>
                        <td><img :src="data.path" alt="Image" style="border-radius: 10px; margin-right: 10px;"
                                width="200px" height="80px" /></td>
                        <td>{{ data.name }}</td>
                        <td>{{ data.dimmensions }}</td>
                        <td>{{ data.size }}Kb</td>
                    </tr>
                </tbody>
            </table>
        </div>
    </div>
</template>

<script setup>
import { ref, computed, onMounted } from 'vue';

const importDocuments = () => {
    alert("Import documents clicked");
}

const dataImage = [
    {
        "id": 1,
        "name": "Seasandiego.jpg",
        "dimmensions": "2000X2000",
        "size": 763.3,
        "path": "https://images.pexels.com/photos/1578750/pexels-photo-1578750.jpeg?auto=compress&cs=tinysrgb&w=600",
        "folder_id": 4
    },
    {
        "id": 2,
        "name": "iMactwin.png",
        "dimmensions": "2000X2000",
        "size": 640.2,
        "path": "https://images.pexels.com/photos/631986/pexels-photo-631986.jpeg?auto=compress&cs=tinysrgb&w=600",
        "folder_id": 4
    },
    {
        "id": 3,
        "name": "MS-Surface.jpg",
        "dimmensions": "2000X2000",
        "size": 113.2,
        "path": "https://images.pexels.com/photos/35600/road-sun-rays-path.jpg?auto=compress&cs=tinysrgb&w=600",
        "folder_id": 4
    },
    {
        "id": 4,
        "name": "SeasandiegoKliazia.jpg",
        "dimmensions": "2000X2000",
        "size": 763.3,
        "path": "https://images.pexels.com/photos/1578750/pexels-photo-1578750.jpeg?auto=compress&cs=tinysrgb&w=600",
        "folder_id": 2
    },
    {
        "id": 5,
        "name": "iMactwinMocfir.png",
        "dimmensions": "2000X2000",
        "size": 640.2,
        "path": "https://images.pexels.com/photos/631986/pexels-photo-631986.jpeg?auto=compress&cs=tinysrgb&w=600",
        "folder_id": 3
    },
    {
        "id": 6,
        "name": "MS-SurfaceSure.jpg",
        "dimmensions": "2000X2000",
        "size": 113.2,
        "path": "https://images.pexels.com/photos/35600/road-sun-rays-path.jpg?auto=compress&cs=tinysrgb&w=600",
        "folder_id": 2
    }
]
const dataFolder = [
    {
        'id': 1,
        'name': "Uploads",
        'parent_id': null
    },
    {
        'id': 2,
        'name': "Image",
        'parent_id': 1
    },
    {
        'id': 3,
        'name': "Documents",
        'parent_id': 1
    }, 
    
    {
        'id': 4,
        'name': "Videos",
        'parent_id': 1
    }
    , {
        'id': 5,
        'name': "Sources",
        'parent_id': null
    },
    {
        'id': 6,
        'name': "Shared",
        'parent_id': null
    }
]

const selectAll = ref(false)

const parentFolders = computed(() => dataFolder.filter(folder => folder.parent_id === null));

const folderIdClicked = ref(0);
const searchText = ref('');
const searchFileName = ref('');
const dataImageSearch = computed(() => 
  dataImage.filter((image) => {
    return image.folder_id === folderIdClicked.value 
    && image.name.toLowerCase().includes(searchText.value.toLowerCase())
    && image.name.includes(searchFileName.value);
  })
);


const folderDropdowns = ref({});

const toggleDropdown = (folderId) => {
    folderDropdowns.value[folderId] = !folderDropdowns.value[folderId];
}

const getChildFolders = (parentId) => {
    return dataFolder.filter(folder => folder.parent_id === parentId);
}

const maxSize = 2048 * 1024; // 2GB = 2048MB => 2048 * 1024KB

const totalSize = computed(() => {
  return dataImage.reduce((total, image) => total + image.size, 0);
});

const percentUsed = computed(() => {
  return ((totalSize.value / maxSize) * 100).toFixed(2);
});

console.log(totalSize.value, percentUsed.value);


onMounted(() => {
  toggleDropdown(1);
  folderIdClicked.value = 2;
});
</script>

<style>
body {
    margin: 0;
    font-family: Arial, sans-serif;
}

table tr:nth-child(even) {
    background-color: #f3f4f6;
}

table tr:hover {
    background-color: #e0e7ff;
}

li {
    list-style: none;
}
</style>