<script lang="ts">
  import { fade } from 'svelte/transition';
  import Icon from '$lib/components/elements/icon.svelte';
  import {
    type Notification,
    notificationController,
    NotificationType,
  } from '$lib/components/shared-components/notification/notification';
  import { onMount } from 'svelte';
  import { mdiCloseCircleOutline, mdiInformationOutline, mdiWindowClose } from '@mdi/js';

  export let notification: Notification;

  $: icon = notification.type === NotificationType.Error ? mdiCloseCircleOutline : mdiInformationOutline;

  const backgroundColor: Record<NotificationType, string> = {
    [NotificationType.Info]: '#E0E2F0',
    [NotificationType.Error]: '#FBE8E6',
    [NotificationType.Warning]: '#FFF6DC',
  };

  const borderColor: Record<NotificationType, string> = {
    [NotificationType.Info]: '#D8DDFF',
    [NotificationType.Error]: '#F0E8E7',
    [NotificationType.Warning]: '#FFE6A5',
  };

  const primaryColor: Record<NotificationType, string> = {
    [NotificationType.Info]: '#4250AF',
    [NotificationType.Error]: '#E64132',
    [NotificationType.Warning]: '#D08613',
  };

  onMount(() => {
    const timeoutId = setTimeout(discard, notification.timeout);
    return () => clearTimeout(timeoutId);
  });

  const discard = () => {
    notificationController.removeNotificationById(notification.id);
  };

  const handleClick = () => {
    const action = notification.action;
    if (action.type === 'discard') {
      discard();
    } else if (action.type == 'link') {
      window.open(action.target);
    }
  };
</script>

<!-- svelte-ignore a11y-no-static-element-interactions -->
<div
  transition:fade={{ duration: 250 }}
  style:background-color={backgroundColor[notification.type]}
  style:border-color={borderColor[notification.type]}
  class="border z-[999999] mb-4 min-h-[80px] w-[300px] rounded-2xl p-4 shadow-md hover:cursor-pointer"
  on:click={handleClick}
  on:keydown={handleClick}
>
  <div class="flex justify-between">
    <div class="flex place-items-center gap-2">
      <Icon path={icon} color={primaryColor[notification.type]} size="20" />
      <h2 style:color={primaryColor[notification.type]} class="font-medium" data-testid="title">
        {notification.type.toString()}
      </h2>
    </div>
    <button on:click|stopPropagation={discard}>
      <Icon path={mdiWindowClose} size="20" />
    </button>
  </div>

  <p class="whitespace-pre-wrap pl-[28px] pr-[16px] text-sm" data-testid="message">
    {notification.message}
  </p>
</div>
