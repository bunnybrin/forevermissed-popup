<template>
  <div class="invite-form">
    <BaseModal
        ref="popup"
        title="Invite others"
    >
      <template #content>
        <div class="invite-form__add">
          <InputField
              ref="inviteEmailInput"
              placeholder="Enter people E-mails"
              :validations="['email']"
              v-model:errors="inputEmail.errors"
              v-model:value="inputEmail.value"
              @save="addInviteEmail"
          />
          <button @click="addInviteEmail" class="btn"> Add</button>

          <div class="invite-form__social">
            <span class="social-text"> or add from</span>
            <SocialList/>
          </div>
        </div>

        <ul class="invite-emails" ref="scroll-view">
          <li
              v-for="(invite, index) in inviteData"
              :class="['invite-emails__item', {'invite-emails__item--with-name': invite.name}]"
          >
            <template v-if="invite.name">
              <span class="invite-emails__mail">{{ invite.name }}</span>
              <span class="invite-emails__name">{{ invite.mail }}</span>
            </template>
            <span v-else class="invite-emails__mail">{{ invite.mail }}</span>

            <DropdownHandler
                ref="dropdown"
                :title="getDefaultRole(invite.role)"
                @click="openDropdown($event, index, invite.role)"
            />

            <IconCircleMinus class="invite-emails__remove" @click="removeInviteEmail(index)"/>
          </li>
        </ul>
        <RolesInfo
            v-if="dropdownIsOpen"
            :style="getStyleForDropdown"
            :roles="rolesData"
            :activeRole="getDefaultRole(selectedRole)"
            @selectRole="selectRole"
        />
      </template>

      <template #footer="{confirm: confirmPopup}" v-if="inviteData.length">
        <InputField
            placeholder="Personal message (optional)"
            v-model:value="personalMessage"
        />
        <div class="invite-form__actions">
          {{ getRecipientCountsText }}
          <button class="btn btn-invert" @click="confirmPopup({inviteData, personalMessage})">Send</button>
        </div>
      </template>
    </BaseModal>
  </div>
</template>

<script>
import InputField from '@/components/core/ui/form-elements/InputField.vue';
import BaseModal from '@/components/core/ui/popups/BaseModal.vue';
import IconCircleMinus from '@/components/icons/IconCircleMinus.vue';
import SocialList from '@/components/invite-others/SocialList.vue';
import DropdownHandler from '@/components/core/ui/dropdowns/DropdownHandler.vue';
import RolesInfo from '@/components/invite-others/RolesInfo.vue';

export default {
  name: 'InvitePopup',
  components: { RolesInfo, DropdownHandler, SocialList, IconCircleMinus, BaseModal, InputField },

  emits: {
    mounted: null,
  },

  data () {
    return {
      inputEmail: {
        value: '',
        errors: [],
      },
      personalMessage: '',
      inviteData: [
        {
          mail: 'example@email.com',
        },
        {
          name: 'John Wick',
          mail: 'example@email.com',
        },
        {
          name: 'Bruce Wayne',
          mail: 'example@email.com',
          role: 'admin',
        },
        {
          name: 'Jose Galdino',
          mail: 'example@email.com',
        },
        {
          mail: 'example@email.com',
        },
        {
          name: 'John Wick',
          mail: 'example@email.com',
        },
        {
          name: 'Peter Parker',
          mail: 'example@email.com',
        },
        {
          name: 'John Doe',
          mail: 'example@email.com',
          role: 'guest',
        },
        {
          name: 'John Wick',
          mail: 'example@email.com',
        },
      ],

      rolesData: [
        {
          id: 'guest',
          title: 'Guest',
          text: 'Default access level, can leave tributes, share media and stories',
        },
        {
          id: 'admin',
          title: 'Administrator',
          text: 'Can control all aspects of the website, including moderating content posted by others and changing website settings',
        },
      ],
      selectedRole: null,

      activeRoleIndex: 0,
      dropdownTop: 0,
      dropdownIsOpen: false,
    };
  },

  computed: {
    getStyleForDropdown () {
      return { top: this.dropdownTop + 'px' };
    },
    getRecipientCountsText () {
      if (!this.inviteData.length) return '';

      return `${this.inviteData.length} ${this.inviteData.length > 1 ? 'recipients' : 'recipient'}`;
    },
  },

  mounted () {
    this.$emit('mounted');
  },
  methods: {
    openPopup () {
      return this.$refs.popup.open();
    },
    getDefaultRole (role) {
      return role || this.rolesData[0].id;
    },
    async addInviteEmail () {
      const isValid = await this.$refs['inviteEmailInput'].validate();

      if (isValid) {
        this.inviteData.push({ mail: this.inputEmail.value, role: 'guest' });
        this.inputEmail.value = '';

        this.scrollToBottom(this.$refs['scroll-view']);
      }

    },

    removeInviteEmail (index) {
      this.inviteData.splice(index, 1);
    },

    scrollToBottom (el) {
      this.$nextTick(() => {
        el.scroll({ top: el.scrollHeight, behavior: 'smooth' });
      });
    },

    openDropdown (e, index, role) {
      if (this.dropdownIsOpen) {
        this.dropdownIsOpen = false;
        return;
      }
      this.selectedRole = this.getDefaultRole(role);
      this.dropdownIsOpen = true;
      this.activeRoleIndex = index;

      const rect = e.currentTarget.getBoundingClientRect();
      this.dropdownTop = rect.top - this.$refs['popup'].$el.firstElementChild.offsetTop;

      this.$refs['scroll-view'].addEventListener('scroll', this.scrollHandler);
    },

    selectRole (role) {
      this.inviteData[this.activeRoleIndex].role = role.id;
      this.dropdownIsOpen = false;
    },

    scrollHandler () {
      this.$refs['scroll-view'].removeEventListener('scroll', this.scrollHandler);
      this.dropdownIsOpen = false;
    },
  },

  watch: {
    dropdownIsOpen () {
      if (this.dropdownIsOpen === false) {
        this.$refs['dropdown'][this.activeRoleIndex].close();
      }
    },
  },
};
</script>

<style lang="scss">
@import "./src/assets/scss/shared/plceholders/scrollbar";

.invite-form {

  &__add {
    display: flex;
    flex-wrap: wrap;
    justify-content: space-between;
    align-items: flex-start;
    position: relative;

    &:after {
      content: '';
      position: absolute;
      left: -24px;
      right: -23px;
      bottom: 0;
      height: 1px;
      width: 100%;
      background-color: var(--bgc-hr);
    }

    .input-field {
      max-width: calc(100% - 91px);
      width: 100%;
    }

    .btn {
      max-width: 80px;
      width: 100%;
      padding-top: 17px;
    }

    .email-list {
      display: flex;
      align-items: center;
      width: 100%;
    }
  }

  &__social {
    display: flex;
    align-items: center;
    margin-top: 15px;
    margin-bottom: 24px;

    .social-text {
      margin-right: 13px;
    }
  }

  &__actions {
    margin-top: 23px;
    text-align: right;
    color: var(--c-secondary-text);
    font-size: 14px;

    .btn-invert {
      max-width: 161px;
      padding-top: 16px;
      width: 100%;
      margin-left: 20px;
    }
  }

  .v-popup__content {
    position: relative;
  }
}

.invite-emails {
  list-style: none;
  margin-right: -17px;
  padding-left: 0;
  display: flex;
  flex-direction: column;
  max-height: 367px;
  overflow: auto;
  @extend %scrollbar;

  &__item {
    max-width: calc(100% - 46px);
    position: relative;
    padding: 13px 16px 13px 12px;
    margin-bottom: 12px;
    border-radius: 6px;
    background-color: var(--bgc-invite-item);

    &--with-name {
      padding-top: 7px;
      padding-bottom: 6px;
    }
  }

  &__name {
    display: block;
    color: var(--c-secondary-text);
    font-size: 12px;
  }

  &__mail {
    display: block;
    font-size: 16px;
  }

  &__remove {
    position: absolute;
    right: -40px;
    top: 50%;
    transform: translate(-50%, -50%);
    fill: #D1CAC1;
    cursor: pointer;
  }

  .v-dropdown {
    position: absolute;
    right: 16px;
    top: 50%;
    transform: translateY(-50%);
  }
}

</style>
