<template>
    <div class="p-4 m-2 bg-slate-800 rounded">
        <h1 class="text-lg">Admin</h1>
        <div class="flex flex-col gap-2" v-if="show">
            <div class="flex flex-col gap-2">
                <p class="text-gray-500 text-sm">Guild IDs</p>
                <div v-for="(guildId, i) in guildIds">
                    <input  class="bg-slate-900 p-1 rounded" :value="guildId" type="text" @input="e => handleGuildChange(i, e)" />
                    <button class="ml-2 bg-slate-900 p-1 px-2 rounded" @click="removeGuild(i)">Remove</button>
                </div>
                <button @click="addGuild">Add</button>
            </div>
            <div class="flex flex-col">
                <label>Member Role ID</label>
                <input v-model="memberRoleId" class="bg-slate-900 rounded p-1" type="text" />
            </div>
            <div class="flex flex-col gap-2">
                <p class="text-gray-500 text-sm">Admins</p>
                <div v-for="(adminId, i) in adminIds">
                    <input  class="bg-slate-900 p-1 rounded" :value="adminId" type="text" @input="e => handleChange(i, e)" />
                    <button class="ml-2 bg-slate-900 p-1 px-2 rounded" @click="removeAdmin(i)">Remove</button>
                </div>
                <button @click="addAdmin">Add</button>
            </div>
            <button class="ml-auto bg-green-900 p-1 px-2 rounded" @click="save">Save</button>
        </div>
        <button @click="show = !show">{{!show ? 'Show' : 'Hide'}}</button>
    </div>
</template>
<script lang="ts" setup>
import api, { Config } from '@/services/api';
import { ref } from 'vue';

const show = ref(false)

const adminIds = ref(null as Config["adminIds"] | null)
const guildIds = ref(null as Config["guildIds"] | null)
const memberRoleId = ref(null as Config["memberRoleId"] | null)

const fillConfig = () => {
    api.getConfig().then(r => {
        adminIds.value = r.adminIds;
        guildIds.value = r.guildIds;
        memberRoleId.value = r.memberRoleId;
    })
}

fillConfig()

const addGuild = () => {
    guildIds.value ??= []
    guildIds.value.push("")
}

const handleGuildChange = (i: number, e: Event) => {
    const id = (e.target as HTMLInputElement).value
    guildIds.value ??= []
    guildIds.value[i] = id
}

const removeGuild = (index: number) => {
    guildIds.value = guildIds.value?.filter((_, i) => i !== index) ?? null
}

const addAdmin = () => {
    adminIds.value ??= []
    adminIds.value.push("")
}

const handleChange = (i: number, e: Event) => {
    const id = (e.target as HTMLInputElement).value
    adminIds.value ??= []
    adminIds.value[i] = id
}

const removeAdmin = (index: number) => {
    adminIds.value = adminIds.value?.filter((_, i) => i !== index) ?? null
}

const save = () => {
    api.setConfig({
        adminIds: adminIds.value ?? [],
        guildIds: guildIds.value ?? [],
        memberRoleId: memberRoleId.value ?? "",
    })
}

</script>