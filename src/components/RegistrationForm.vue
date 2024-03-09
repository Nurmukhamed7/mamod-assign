<template>
    <div class="custom-form">
        <a-form
            @submit.prevent="submitForm"
            ref="registrationForm"
            v-if="!registrationSuccess"
        >
            <h1 class="title">Регистрация</h1>
            <svg
                class="separator"
                xmlns="http://www.w3.org/2000/svg"
                width="960"
                height="2"
                viewBox="0 0 960 2"
                fill="none"
            >
                <path d="M-0.000244141 1H960" stroke="#D9D9D9" />
            </svg>
            <h2 class="subtitle">Заполните Ваши данные</h2>
            <div class="form-wrapper">
                <a-form-item
                    class="name"
                    :validateStatus="validateStatus('name')"
                    :help="validateMessage('name')"
                >
                    <a-input
                        v-model="formData.name"
                        placeholder="Имя"
                    ></a-input>
                </a-form-item>
                <a-form-item
                    class="email"
                    :validateStatus="validateStatus('email')"
                    :help="validateMessage('email')"
                >
                    <a-input
                        v-model="formData.email"
                        placeholder="Email"
                    ></a-input>
                </a-form-item>

                <a-form-item
                    class="select"
                    :validateStatus="validateStatus('position')"
                    :help="validateMessage('position')"
                >
                    <a-select
                        placeholder="Должность"
                        v-model="formData.position"
                    >
                        <a-select-option
                            v-for="option in positions"
                            :key="option.value"
                            :value="option.value"
                            >{{ option.name }}</a-select-option
                        >
                    </a-select>
                </a-form-item>
                <a-form-item
                    class="password"
                    :validateStatus="validateStatus('password')"
                    :help="validateMessage('password')"
                >
                    <a-input
                        :type="passwordFieldType"
                        v-model="formData.password"
                        placeholder="Пароль"
                    >
                        <template #suffix>
                            <a-icon
                                class="pointer"
                                @click="togglePasswordVisibility('password')"
                                :type="
                                    passwordFieldVisible['password']
                                        ? 'eye'
                                        : 'eye-invisible'
                                "
                            />
                        </template>
                    </a-input>
                </a-form-item>
                <a-form-item
                    class="confirm-password"
                    :validateStatus="validateStatus('confirmPassword')"
                    :help="validateMessage('confirmPassword')"
                >
                    <a-input
                        :type="confirmPasswordFieldType"
                        v-model="formData.confirmPassword"
                        placeholder="Повторите пароль"
                    >
                        <template #suffix>
                            <a-icon
                                class="pointer"
                                @click="
                                    togglePasswordVisibility('confirmPassword')
                                "
                                :type="
                                    passwordFieldVisible['confirmPassword']
                                        ? 'eye'
                                        : 'eye-invisible'
                                "
                            />
                        </template>
                    </a-input>
                </a-form-item>
            </div>
            <svg
                class="separator"
                xmlns="http://www.w3.org/2000/svg"
                width="960"
                height="2"
                viewBox="0 0 960 2"
                fill="none"
            >
                <path d="M0 1H960.001" stroke="#D9D9D9" />
            </svg>
            <a-form-item class="switcher">
                <a-switch v-model="formData.profileVisibility"> </a-switch>
                <label>
                    <span
                        >Хотите чтобы Ваш профиль видели другие участники
                        платформы?</span
                    >

                    <br />
                    <span
                        >Включает профиль для просмотра другими пользователями
                        по ссылке</span
                    >
                </label>
            </a-form-item>
            <div class="form-bottom">
                <div class="checkbox-wrapper">
                    <a-form-item>
                        <a-checkbox v-model="formData.agreement"> </a-checkbox>
                    </a-form-item>
                    <label>
                        Регистрируясь, Вы соглашаетесь с
                        <a href="#" target="_blank" rel="noopener noreferrer">
                            политикой конфиденциальности</a
                        >
                        и обработкой
                        <a href="#" target="_blank" rel="noopener noreferrer"
                            >персональных данных</a
                        >
                    </label>
                </div>

                <a-form-item>
                    <a-button type="primary" html-type="submit" class="button"
                        >Зарегистрироваться</a-button
                    >
                </a-form-item>
            </div>
        </a-form>
        <h2 v-if="registrationSuccess" class="success-message">
            Регистрация успешно завершена!
        </h2>
    </div>
</template>

<script>
import axios from 'axios';
import 'ant-design-vue/dist/antd.css';
import {
    Form,
    Input,
    Select,
    Checkbox,
    Button,
    Icon,
    Switch,
} from 'ant-design-vue';

export default {
    components: {
        'a-form': Form,
        'a-form-item': Form.Item,
        'a-input': Input,
        'a-select': Select,
        'a-select-option': Select.Option,
        'a-checkbox': Checkbox,
        'a-switch': Switch,
        'a-button': Button,
        'a-icon': Icon,
    },
    data() {
        return {
            formData: {
                name: '',
                email: '',
                password: '',
                confirmPassword: '',
                position: null,
                agreement: true,
                profileVisibility: false,
            },
            positions: [
                { value: 1, name: 'Frontend разработчик' },
                { value: 2, name: 'Backend разработчик' },
                { value: 3, name: 'Менеджер проекта' },
            ],
            passwordFieldType: 'password',
            confirmPasswordFieldType: 'password',
            passwordFieldVisible: {
                password: false,
                confirmPassword: false,
            },
            registrationSuccess: false,
            errors: {
                name: false,
                email: false,
                password: false,
                confirmPassword: false,
                position: false,
            },
            errorMessages: {
                name: '',
                email: '',
                password: '',
                confirmPassword: '',
                position: '',
            },
        };
    },
    methods: {
        async submitForm() {
            const validateFields = [
                'name',
                'email',
                'password',
                'confirmPassword',
                'position',
            ];

            const hasErrors = validateFields.some((field) => {
                if (!this.formData[field]) {
                    this.errors[field] = true;
                    this.errorMessages[field] =
                        'Это поле обязательно для заполнения.';
                    return true;
                } else {
                    this.errors[field] = false;
                    this.errorMessages[field] = '';
                }
            });
            // Проверка корректности email
            if (!this.validateEmail(this.formData.email)) {
                this.errors.email = true;
                this.errorMessages.email = 'Введите корректный email адрес.';
                return;
            } else {
                this.errors.email = false;
                this.errorMessages.email = '';
            }
            // Проверка совпадения паролей
            if (!hasErrors) {
                if (this.formData.password !== this.formData.confirmPassword) {
                    this.errors.confirmPassword = true;
                    this.errorMessages.confirmPassword = 'Пароли не совпадают.';
                    return;
                }

                if (!this.formData.agreement) {
                    alert(
                        'Необходимо согласие на обработку персональных данных'
                    );
                } else {
                    const userData = {
                        public: true,
                        username: this.formData.name,
                        role: this.formData.position,
                        email: this.formData.email,
                        password: this.formData.password,
                        password_repeat: this.formData.confirmPassword,
                    };
                    try {
                        const response = await axios.post(
                            'https://6545e498fe036a2fa954ec57.mockapi.io/users',
                            userData
                        );
                        if (response.status === 201) {
                            this.registrationSuccess = true;
                        }
                    } catch (error) {
                        console.error('Ошибка при регистрации:', error);
                    }
                }
            }
        },
        togglePasswordVisibility(field) {
            this.passwordFieldVisible[field] =
                !this.passwordFieldVisible[field];
            const fieldType = this.passwordFieldVisible[field]
                ? 'text'
                : 'password';
            this.passwordFieldType =
                field === 'password' ? fieldType : this.passwordFieldType;
            this.confirmPasswordFieldType =
                field === 'confirmPassword'
                    ? fieldType
                    : this.confirmPasswordFieldType;
        },
        validateEmail(email) {
            const emailRegex =
                /^[a-zA-Z0-9._-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,4}$/;
            return emailRegex.test(email);
        },
        validateStatus(field) {
            return this.errors[field] ? 'error' : '';
        },
        validateMessage(field) {
            return this.errorMessages[field];
        },
    },
};
</script>
