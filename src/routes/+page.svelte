<script lang="ts">
  import { onMount } from 'svelte';
  import dayjs from 'dayjs';

  let currentTime = $state(dayjs('2025-12-01 09:59:48'));

  type CurrentState = 'notyet' | 'working' | 'resting';
  let currentState: CurrentState = $state('notyet');

  const toWorking = () => (currentState = 'working');
  const toResting = () => (currentState = 'resting');
  const toNotyet = () => (currentState = 'notyet');

  type CommentType = 'normal' | 'system';

  type Comment = {
    text: string;
    type: CommentType;
  };

  let commentArea;
  const comments: Comment[] = $state([]);

  const addComment = (text: string, type: CommentType, timeout: number) => {
    setTimeout(() => {
      comments.push({ text, type });
      setTimeout(() => {
        commentArea.scroll({ top: commentArea.scrollHeight, behavior: 'smooth' });
      }, 0);
    }, timeout);
  };

  onMount(() => {
    setInterval(() => {
      currentTime = currentTime.add(1, 's');
    }, 1000);
    addComment('これ間に合う？', 'normal', 1000);
    addComment('遅刻っぽ', 'normal', 1700);
    addComment('ざわ・・・ざわ・・・', 'normal', 2900);
  });
</script>

<div class="p-20">
  <div class="border rounded-lg grid grid-cols-3 max-w-200">
    <div class="col-span-2 text-center py-5">
      <p>
        {currentTime.format('YYYY年MM月DD日')}
      </p>
      <p class="text-4xl bold">
        {currentTime.format('hh:mm:ss')}
      </p>
      <div class="mt-10 flex justify-center gap-5">
        {#if currentState === 'notyet'}
          <button
            class="w-30 h-15 text-xl text-white bg-blue-500 hover:bg-blue-700 active:bg-blue-500 transition rounded-sm"
            onclick={toWorking}
          >
            出勤
          </button>
        {:else if currentState === 'working'}
          <button
            class="w-30 h-15 text-xl text-white bg-red-500 hover:bg-red-700 active:bg-red-500 transition rounded-sm"
            onclick={toNotyet}
          >
            退勤
          </button>
          <button
            class="w-30 h-15 text-xl border bg-white hover:bg-gray-100 active:bg-white transition rounded-sm"
            onclick={toResting}
          >
            休憩開始
          </button>
        {:else if currentState === 'resting'}
          <button class="w-30 h-15 text-xl text-white bg-gray-300 transition rounded-sm">
            退勤
          </button>
          <button
            class="w-30 h-15 text-xl border bg-white hover:bg-gray-100 active:bg-white transition rounded-sm"
            onclick={toWorking}
          >
            休憩終了
          </button>
        {/if}
      </div>
      <p class="mt-10 text-gray-500">勤怠修正　各種申請　メモ</p>
    </div>
    <div class="border-l p-5 h-100 overflow-scroll" bind:this={commentArea}>
      {#each comments as { text, type }}
        <p class="border-b border-gray-400">
          {text}
        </p>
      {/each}
    </div>
  </div>
</div>
