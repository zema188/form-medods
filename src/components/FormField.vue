<template>
    <div class="field-w"
        :class="data.customClass ? [...data.customClass] : ''"
    >
        <div class="field__text">
            <span class="field__subtitle">
                {{ data.name }}<b v-if="data.required">*</b>
            </span>
            <span class="error"
                v-if="data.error"
            >
                <span class="error" v-if="!data.textError">
                    Поле обязательно для заполнения
                </span>
                <span class="error" v-else>
                    {{ data.textError }}
                </span>
            </span>
        </div>
        <div class="field"
            v-if="data.field === 'input' && data.type !== 'radio' && data.type !== 'checkbox'"
        >
            <input ref="input"
                :name="data.name"
                :type="data.type"
                :value="data.value"
                @input="event => handleInput(event.target.value)"
            />
        </div>

        <div class="field" v-if="data.customClass?.includes('gender')">
            <label v-for="(item, index) of data.options" :key="index">
                <input 
                    :name="data.name"
                    :type="data.type"
                    :value="item"
                    v-model="data.value"
                />
                {{ item }}
            </label>
        </div>

        <div class="field"
            v-if="data.field === 'select'"
        >
            <select
                v-model="data.value"
                :multiple="data.multiple"
                :name="data.name"
            >
                <option v-for="(item, index) of data.options"
                    :key="index"
                    :value="item"
                >
                    {{ item }}
                </option>
            </select>
        </div>

        <div class="field"
            v-if="data.type === 'checkbox'"
        >
            <input ref="input"
                :name="data.name"
                :type="data.type"
                v-model="data.value"
            />
        </div>
    </div>
</template>

<script>

export default {
    name: 'FormField',

    props: {
        data: {
            type: Object,
            required: true
        }
    },

    data() {
        return {
            input: null
        }
    },

    methods: {
        handleInput: function(value) {
        
            if(this.data.customClass?.includes('phone')) {
                this.setMask()
                return
            }

            this.$emit('update:value', value)
        },

        setMask: function() {
            let matrix = '7 ### ### ## ##'
            let input = this.$refs.input
            

            if(input.value.length > matrix.length) {
                input.value = input.value.slice(0, matrix.length)
                this.$emit('update:value', input.value.slice(0, matrix.length))
                return
            }

            if(!input.value) {
                input.value = 7
                this.$emit('update:value', 7)
                return
            }

            let i = 0,
                val = input.value.replace(/\D/g, '')

            const newValue = matrix.replace(/(?!\+)./g, function(a) {
                return /[#\d]/.test(a) && i < val.length ? val.charAt(i++) : i >= val.length ? '' : a
            });

            this.$emit('update:value', newValue)
        }
    },

    mounted() {
        if(this.data.customClass?.includes('phone')) this.setMask()
    },
}
</script>

<style scoped lang="scss">
.field-w {
    width: calc(50% - 10px);
    display: flex;
    flex-direction: column;
    @media (max-width: 539px) {
        width: 100%;
    }
    &.gender {
        & .field {
            display: flex;
            gap: 15px;
        }
        & label {
            display: flex;
            align-items: center;
            gap: 5px;
        }
    }
    &.select {
        & .field {
        }
        & select {
            width: 100%;
            height: 35px;
            background: #dedede;
            padding: 1px 10px;
        }
    }
    &.checkbox {
        & .field {
        }
    }
    &.sms {
        flex-direction: row-reverse;
        gap: 10px;
        & .field__text {
            flex: 1;
        }
        & .field {
            width: fit-content;
            flex: 0;
            & input {
                width: auto;
                height: auto;
            }
        }
    }
}

.field {
    flex: 1;
    &__text {
        line-height: 120%;
    }
    &__subtitle {
        font-size: 12px;
    }

}
.error {
    color: red;
    font-size: 12px;
}
input {
    width: 100%;
    height: 100%;
    border-radius: 12px;
    padding: 5px;
    height: 35px;
    border: 2px solid #969696;
    &:focus {
        border-color: rgb(38, 194, 255);
    }
}
</style>
  