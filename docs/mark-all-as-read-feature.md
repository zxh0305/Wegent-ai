# Group Chat "Mark All as Read" Feature

## Overview
This feature allows users to mark all unread group chats as read with a single click, improving the user experience for managing multiple group conversations.

## Implementation Details

### UI Components

#### TaskSidebar.tsx
Location: `frontend/src/features/tasks/components/sidebar/TaskSidebar.tsx`

**Group Chat Section (lines 658-671)**
- Displays a "全部标记为已读" (Mark All as Read) button when there are unread group chats
- Shows the count of unread group chats in parentheses
- Button is only visible when `unreadGroupChats.length > 0`

```typescript
{unreadGroupChats.length > 0 && (
  <button
    onClick={handleMarkGroupTasksAsViewed}
    className="text-xs text-text-muted hover:text-text-primary transition-colors"
  >
    {t('common:tasks.mark_all_read')} ({unreadGroupChats.length})
  </button>
)}
```

**Personal Tasks Section (lines 810-817)**
- Similar functionality for personal tasks
- Displays "全部标记为已读" button when there are unread personal tasks

```typescript
{getUnreadCount(filteredPersonalTasks) > 0 && (
  <button
    onClick={handleMarkPersonalTasksAsViewed}
    className="text-xs text-text-muted hover:text-text-primary transition-colors"
  >
    {t('common:tasks.mark_all_read')} ({getUnreadCount(filteredPersonalTasks)})
  </button>
)}
```

### Context and State Management

#### TaskContext.tsx
Location: `frontend/src/features/tasks/contexts/taskContext.tsx`

**Key Functions:**

1. **handleMarkGroupTasksAsViewed (lines 846-850)**
   - Marks all group tasks as viewed
   - Triggers re-render by incrementing `viewStatusVersion`

```typescript
const handleMarkGroupTasksAsViewed = () => {
  markAllTasksAsViewed(groupTasks)
  setViewStatusVersion(prev => prev + 1)
}
```

2. **handleMarkPersonalTasksAsViewed (lines 853-857)**
   - Marks all personal tasks as viewed
   - Triggers re-render by incrementing `viewStatusVersion`

```typescript
const handleMarkPersonalTasksAsViewed = () => {
  markAllTasksAsViewed(personalTasks)
  setViewStatusVersion(prev => prev + 1)
}
```

3. **markAllTasksAsViewed**
   - Core function that marks tasks as viewed using localStorage
   - Updates view status for each task in the provided list

### Internationalization

**Chinese (zh-CN):**
Location: `frontend/src/i18n/locales/zh-CN/common.json`
```json
{
  "mark_all_read": "全部标记为已读"
}
```

**English (en):**
Location: `frontend/src/i18n/locales/en/common.json`
```json
{
  "mark_all_read": "Mark all as read"
}
```

### Task Unread Detection

**Utility Function:**
Location: `frontend/src/utils/taskViewStatus.ts`

The `isTaskUnread` function checks if a task is unread by comparing its `updated_at` timestamp with the last viewed timestamp stored in localStorage.

## User Flow

1. User navigates to the chat/task page
2. Sidebar displays group chats section with unread count
3. When there are unread group chats, a "全部标记为已读" (count) button appears
4. User clicks the button
5. All unread group chats are marked as viewed
6. Unread indicators disappear immediately
7. Button is hidden (no unread group chats remaining)

## Benefits

- **Efficiency**: Quickly clear all unread indicators without clicking each conversation
- **User Experience**: Reduces cognitive load when managing many group chats
- **Consistency**: Same behavior for both group chats and personal tasks

## Testing

### Manual Testing Steps:
1. Open the application
2. Ensure there are multiple unread group chats
3. Navigate to the sidebar
4. Verify the "全部标记为已读" button is visible with correct count
5. Click the button
6. Verify all unread indicators disappear
7. Verify the button is hidden after marking all as read

### Edge Cases:
- No unread group chats → Button should not be visible
- Single unread group chat → Button should be visible with count (1)
- All group chats marked as read → Button should disappear

## Future Enhancements

Possible improvements:
- Add confirmation dialog before marking all as read (optional)
- Add undo functionality
- Add bulk actions (e.g., mark specific subset as read)
- Add filter to show only unread tasks

## Notes

- This feature is currently implemented and working in the main branch
- The feature uses localStorage for persisting view status
- View status is per-user and per-browser
- The feature automatically updates the UI when tasks are marked as read
