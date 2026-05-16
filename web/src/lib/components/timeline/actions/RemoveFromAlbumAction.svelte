<script lang="ts">
  import MenuOption from '$lib/components/shared-components/context-menu/menu-option.svelte';
  import { assetMultiSelectManager } from '$lib/managers/asset-multi-select-manager.svelte';
  import { removeAssetsFromAlbum } from '$lib/utils/album-utils';
  import { getAlbumInfo, type AlbumResponseDto } from '@immich/sdk';
  import { IconButton } from '@immich/ui';
  import { mdiDeleteOutline, mdiImageRemoveOutline } from '@mdi/js';
  import { t } from 'svelte-i18n';

  interface Props {
    album: AlbumResponseDto;
    onRemove: ((assetIds: string[]) => void) | undefined;
    assetIds?: string[];
    menuItem?: boolean;
  }

  let { album = $bindable(), onRemove, assetIds, menuItem = false }: Props = $props();

  const removeFromAlbum = async () => {
    const ids = assetIds ?? assetMultiSelectManager.assets.map(({ id }) => id) ?? [];
    const removedIds = await removeAssetsFromAlbum(album.id, ids);
    if (!removedIds) {
      return;
    }
    album = await getAlbumInfo({ id: album.id });
    onRemove?.(removedIds);
    assetMultiSelectManager.clear();
  };
</script>

{#if menuItem}
  <MenuOption text={$t('remove_from_album')} icon={mdiImageRemoveOutline} onClick={removeFromAlbum} />
{:else}
  <IconButton
    shape="round"
    color="secondary"
    variant="ghost"
    aria-label={$t('remove_from_album')}
    icon={mdiDeleteOutline}
    onclick={removeFromAlbum}
  />
{/if}
