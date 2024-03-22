<template>
    <form class="form"
        @submit.prevent="sendForm($event.target)"
        @keydown.enter="sendForm($event.target)"
    >
        <div class="form__list">
            <form-field
                v-for="(field, index) of attributes" :key="index"
                :data="field"
                @update:value="field.value = $event"
            />
        </div>

        <h2>
            Адрес
        </h2>

        <div class="form__list">
            <form-field
                v-for="(field, index) of address" :key="index"
                :data="field"
                @update:value="field.value = $event"
            />
        </div>

        <h2>
            Паспорт
        </h2>

        <div class="form__list">
            <form-field
                v-for="(field, index) of passport" :key="index"
                :data="field"
                @update:value="field.value = $event"
            />
        </div>

        <button class="form__btn-send btn btn_green" type="submit">
            Отправить
        </button>
    </form>
</template>

<script>
import FormField from './FormField.vue'

export default {

    name: 'TheForm',

    components: { FormField },

    data() {
        return {
            attributes: {
                lastName: {
                    field: 'input',
                    type: 'text',
                    required: true,
                    value: '',
                    name: 'Фамилия',
                    error:  false,
                },

                firstName: {
                    field: 'input',
                    type: 'text',
                    required: true,
                    value: '',
                    name: 'Имя',
                    error:  false,
                },

                surname: {
                    field: 'input',
                    type: 'text',
                    value: '',
                    name: 'Отчество',
                    error:  false,
                },

                birthday: {
                    field: 'input',
                    type: 'date',
                    required: true,
                    value: null,
                    name: 'Дата рождения',
                    error:  false,
                },

                phone: {
                    field: 'input',
                    type: 'text',
                    required: true,
                    value: '',
                    name: 'Номер телефона',
                    error:  false,
                    textError: '',
                    customClass: ['phone'],
                },

                gender: {
                    field: 'input',
                    type: 'radio',
                    value: '',
                    options: ['Мужской', 'Женский'],
                    name: 'Пол',
                    customClass: ['gender'],
                    error:  false,
                },

                clientGroup: {
                    field: 'select',
                    multiple: 'true',
                    value: [''],
                    options: ['VIP', 'Проблемные', 'ОМС'],
                    required: true,
                    name: 'Группа клиентов',
                    customClass: ['select'],
                    error:  false,
                },

                doctor: {
                    field: 'select',
                    value: 'Врач не выбран',
                    options: ['Врач не выбран', 'Иванов', 'Захаров', 'Чернышева'],
                    name: 'Лечащий врач',
                    customClass: ['select'],
                    error:  false,
                },

                sms: {
                    field: 'input',
                    type: 'checkbox',
                    value: '',
                    name: 'Не отправлять СМС',
                    customClass: ['sms'],
                    error:  false,
                },
            },

            address: {
                index: {
                    field: 'input',
                    type: 'number',
                    value: '',
                    name: 'Индекс',
                    error:  false,
                },
                country: {
                    field: 'input',
                    type: 'text',
                    value: '',
                    name: 'Страна',
                    error:  false,
                },
                region: {
                    field: 'input',
                    type: 'text',
                    value: '',
                    name: 'Область',
                    error:  false,
                },
                city: {
                    field: 'input',
                    type: 'text',
                    required: true,
                    value: '',
                    name: 'Город',
                    error:  false,
                },
                street: {
                    field: 'input',
                    type: 'text',
                    value: '',
                    name: 'Улица',
                    error:  false,
                },
                house: {
                    field: 'input',
                    type: 'text',
                    value: '',
                    name: 'Дом',
                    error:  false,
                },
            },

            passport: {
                typeDocemunt: {
                    field: 'select',
                    value: 'Паспорт',
                    options: ['Паспорт', 'Свидетельство о рождении', 'Вод. удостоверение'],
                    name: 'Тип документа',
                    customClass: ['select'],
                    error:  false,
                },
                passportSerial : {
                    field: 'input',
                    type: 'text',
                    value: '',
                    name: 'Серия',
                    error:  false,
                },
                passportNumber: {
                    field: 'input',
                    type: 'text',
                    value: '',
                    name: 'Номер',
                    error:  false,
                },
                issuedBy: {
                    field: 'input',
                    type: 'text',
                    value: '',
                    name: 'Кем выдан',
                    error:  false,
                },
                dateOfIssue: {
                    field: 'input',
                    type: 'date',
                    required: true,
                    value: '',
                    name: 'Дата выдачи',
                    error:  false,
                },
            },
        }
    },

    methods: {
        sendForm: async function(form) {
            if(this.validateForm() > 0) return

            const formData = new FormData(form);

            try {
                const response = await fetch("api/post", {
                    method: "POST",
                    body: formData,
                });

                this.$emit('success')

            } catch (e) {
                console.error(e);
            }
        },

        validateForm: function() {
            let errors = 0
            
            for (const[key, value] of Object.entries(this.allFeilds)) {
                if(value.required) {
                    value.error = !value.value
                    if(value.error) errors++
                }

                if(value.phone) {
                    const length = value.value.toString().replace(/ /g,'').length
                    if(length === 1) {
                        value.error = true
                        value.textError = ''
                        errors++
                    } else if(length < 11) {
                        value.error = true
                        value.textError = 'Телефон должен состоять из 11 цифр'
                        errors++
                    } else {
                        value.error = false
                        value.textError = ''
                        errors++
                    }
                }
            }
            return errors
        }
    },

    computed: {
        allFeilds: function() {
            return {
                ...this.attributes,
                ...this.address,
                ...this.passport,
            }
        }
    }
}
</script>

<style scoped lang="scss">
.form {
    width: 100%;
    max-width: 700px;
    border: 1px solid #969696;
    border-radius: 12px;
    padding: 20px;
    &__list {
        display: flex;
        flex-wrap: wrap;
        gap: 20px;
        margin: 0 auto 20px;
        @media (max-width: 539px) {
            gap: 10px;
        }
    }
    & h2 {
        font-weight: bold;
        text-align: center;
        font-size: 25px;
        margin-bottom: 15px;
    }

    &__btn-send {
        width: 100%;
    }
}
</style>
  