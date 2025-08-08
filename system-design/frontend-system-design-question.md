
## Application Design Questions

### 1. **Design a News Feed (Facebook/Twitter)**
**Question:** Design a news feed system like Facebook or Twitter that displays posts from users and their connections.

**Answer Approach:**
- Focus on the most unique aspects like news feed, pagination of the feed, and creating new posts
- Key considerations: infinite scrolling, real-time updates, optimistic UI updates
- Architecture: Component hierarchy (Feed Container → Post List → Individual Posts)
- Data management: Normalized state structure, caching strategies
- API design: Pagination (cursor-based vs offset), WebSocket for real-time updates
- Performance: Virtual scrolling, lazy loading images, request debouncing

### 2. **Design YouTube Video Player & Platform**
**Question:** Design YouTube - one of the most common interview questions in frontend system design interviews

**Answer Approach:**
- Video player implementation using HTML video tag and MediaSource API
- Core components: Video player controls, comment system, recommendation sidebar
- Live commenting system with real-time updates
- Adaptive bitrate streaming (HLS/DASH)
- Client-side caching and offline support
- Performance optimization: lazy loading thumbnails, prefetching video segments

### 3. **Design an E-commerce Marketplace (Amazon/eBay)**
**Question:** Design an e-commerce marketplace with product listings, cart, and checkout.

**Answer Approach:**
- Component architecture: Product catalog, shopping cart, checkout flow
- State management: Cart persistence, inventory sync
- Search and filtering: Client-side vs server-side filtering
- Payment integration considerations
- Performance: Image optimization, lazy loading, CDN usage
- Security: XSS prevention, secure payment handling

### 4. **Design Instagram/Photo Sharing App**
**Question:** Design a photo sharing application like Instagram

**Answer Approach:**
- Image upload and processing pipeline
- Feed algorithm and infinite scroll
- Real-time features: likes, comments, stories
- Image optimization: multiple resolutions, lazy loading
- Offline capabilities with service workers
- Performance: Virtual scrolling for large feeds

### 5. **Design a Chat/Messaging Application (WhatsApp/Messenger)**
**Question:** Design WhatsApp/Messenger - Very Important if you are preparing for Meta interview rounds

**Answer Approach:**
- Real-time messaging architecture using WebSockets
- Message delivery status (sent, delivered, read)
- End-to-end encryption considerations
- Offline message queue and sync
- Media sharing and compression
- Typing indicators and presence system

### 6. **Design a Video Conferencing App (Zoom/Google Meet)**
**Question:** Design a video conferencing app like Zoom

**Answer Approach:**
- WebRTC implementation for peer-to-peer communication
- Screen sharing capabilities
- Grid vs speaker view layouts
- Bandwidth adaptation and quality control
- Meeting controls: mute, video toggle, participant management
- Performance: CPU/GPU optimization for video rendering

### 7. **Design a Travel Booking Platform (Airbnb/Expedia)**
**Question:** Design a travel booking website like Airbnb and Expedia

**Answer Approach:**
- Search interface with filters and date pickers
- Map integration for location-based search
- Booking flow and calendar management
- Price calculation and availability checking
- Review and rating system
- Responsive design for mobile users

### 8. **Design a Music Streaming Service (Spotify/Pandora)**
**Question:** Design a music streaming website like Spotify and Pandora

**Answer Approach:**
- Audio player implementation with playlist support
- Offline playback with service workers
- Recommendation system UI
- Search with debouncing
- Queue management
- Performance: Audio buffering strategies

### 9. **Design an Email Client (Gmail/Outlook)**
**Question:** Design a desktop email client like Microsoft Outlook and Apple Mail

**Answer Approach:**
- Inbox organization (folders, labels, filters)
- Rich text editor for composing
- Search functionality
- Attachment handling
- Real-time sync across devices
- Offline capabilities

### 10. **Design a Collaborative Document Editor (Google Docs)**
**Question:** Design a real-time collaborative document editor

**Answer Approach:**
- Operational Transformation (OT) or CRDTs for conflict resolution
- Real-time cursor positions and selections
- Version history and rollback
- Commenting and suggestion features
- Performance: Efficient diff algorithms
- Offline editing with sync

## UI Component Design Questions

### 11. **Design an Autocomplete/Typeahead Component**
**Question:** Design a search autocomplete component with suggestions

**Answer Approach:**
- API design: configuration options, props in React, following Open-closed principle
- Debouncing user input
- Keyboard navigation (arrow keys, enter, escape)
- Accessibility: ARIA attributes, screen reader support
- Caching strategies for suggestions
- Performance: Virtual scrolling for large result sets

### 12. **Design an Image Carousel/Gallery**
**Question:** Build an image carousel with touch support and lazy loading

**Answer Approach:**
- Touch gestures and mouse events
- Infinite scrolling vs finite
- Lazy loading images
- Thumbnail navigation
- Responsive design
- Performance: Preloading adjacent images

### 13. **Design a Modal/Dialog Component**
**Question:** Design a modal/dialog component that shows a window overlaying the contents on the page

**Answer Approach:**
- Focus management and keyboard trapping
- Backdrop click handling
- Nested modal support
- Animation and transitions
- Accessibility: ARIA roles, focus restoration
- Z-index management

### 14. **Design a Dropdown Menu Component**
**Question:** Design a dropdown menu component that reveals a menu containing a list of actions

**Answer Approach:**
- Positioning strategies (absolute, fixed, portal)
- Keyboard navigation
- Multi-level support
- Click outside detection
- Accessibility features
- Mobile touch support

### 15. **Design a Data Table/Grid Component**
**Question:** Build a performant data table with sorting, filtering, and pagination

**Answer Approach:**
- Virtual scrolling for large datasets
- Column resizing and reordering
- Sort indicators and multi-column sorting
- Client vs server-side operations
- Responsive design strategies
- Performance: Memoization, windowing

### 16. **Design a Form Builder Component**
**Question:** Create a dynamic form builder with validation

**Answer Approach:**
- Drag-and-drop field arrangement
- Dynamic validation rules
- Conditional logic for fields
- Form state management
- Error handling and display
- Accessibility compliance

### 17. **Design a Calendar/Date Picker Component**
**Question:** Build a calendar component with date selection and navigation

**Answer Approach:**
- Internationalization: RTL language support for Arabic and Hebrew
- Date range selection
- Keyboard navigation
- Mobile-friendly interface
- Timezone handling
- Performance: Efficient date calculations

### 18. **Design a File Upload Component**
**Question:** Create a file upload component with progress tracking

**Answer Approach:**
- Drag-and-drop support
- Multiple file selection
- Progress indicators
- File type validation
- Chunked uploads for large files
- Error handling and retry logic

### 19. **Design an Infinite Scroll Component**
**Question:** Build an infinite scroll list with optimal performance

**Answer Approach:**
- Intersection Observer API usage
- Virtual scrolling implementation
- Loading states and placeholders
- Error handling and retry
- Bi-directional scrolling
- Memory management

### 20. **Design a Tree View Component**
**Question:** Create a hierarchical tree component with expand/collapse

**Answer Approach:**
- Recursive rendering strategy
- Lazy loading of child nodes
- Keyboard navigation
- Drag-and-drop support
- Search and filter capabilities
- Performance: Virtualization for large trees

## Advanced System Design Questions

### 21. **Design a Real-time Analytics Dashboard**
**Question:** Build a dashboard showing live metrics and charts

**Answer Approach:**
- WebSocket connections for real-time data
- Efficient chart rendering and updates
- Data aggregation on client
- Time-series data handling
- Performance: Throttling updates, canvas rendering
- Responsive layout system

### 22. **Design a Code Editor (CodePen/CodeSandbox)**
**Question:** Create an in-browser code editor with live preview

**Answer Approach:**
- Syntax highlighting implementation
- Code completion and IntelliSense
- Live preview with sandboxing
- Multi-file support
- Version control integration
- Performance: Web Workers for parsing

### 23. **Design a Drawing/Diagram Tool (Lucidchart)**
**Question:** Design a design tool like Lucidchart

**Answer Approach:**
- Canvas vs SVG rendering choices
- Shape manipulation and selection
- Undo/redo functionality
- Collaboration features
- Export functionality
- Performance: Render optimization

### 24. **Design a Maps Application**
**Question:** Build a maps interface with markers and interactions

**Answer Approach:**
- Tile loading and caching
- Marker clustering for performance
- Location-based databases and Hilbert curves
- Offline map support
- Route visualization
- Performance: Level of detail rendering

### 25. **Design a Stock Trading Platform**
**Question:** Design real-time stock price feed system

**Answer Approach:**
- Real-time price updates via WebSocket
- Interactive charts with zoom/pan
- Order placement UI
- Portfolio visualization
- Market depth display
- Performance: Efficient data updates

### 26. **Design a Gaming Platform UI**
**Question:** Design Chess/Gaming website - Very Important for Meta interviews

**Answer Approach:**
- Real-time game state synchronization
- Turn-based game mechanics
- Spectator mode
- Tournament brackets
- Chat integration
- Performance: Optimistic updates

### 27. **Design a Social Media Stories Feature**
**Question:** Build a stories feature like Instagram/Snapchat

**Answer Approach:**
- Story viewer with progress indicators
- Touch gestures for navigation
- Auto-advance timing
- Media preloading
- Analytics tracking
- Performance: Memory management

### 28. **Design a Notification System**
**Question:** Create a multi-channel notification system

**Answer Approach:**
- Real-time updates, WebSocket integration
- Notification grouping and prioritization
- Read/unread status management
- Push notification integration
- Notification preferences UI
- Performance: Batching and throttling

### 29. **Design a Search Interface**
**Question:** Build a comprehensive search experience

**Answer Approach:**
- Search suggestions and autocomplete
- Faceted search with filters
- Search history
- Advanced query builder
- Results ranking display
- Performance: Debouncing, caching

### 30. **Design a Content Management System**
**Question:** Content Management System Design

**Answer Approach:**
- WYSIWYG editor integration
- Asset management
- Version control
- Publishing workflow
- Multi-language support
- Performance: Lazy loading content

## Mobile-Specific Design Questions

### 31. **Design a Progressive Web App (PWA)**
**Question:** Convert a web app to work offline-first

**Answer Approach:**
- Service worker implementation
- Mobile constraints: less powerful hardware, smaller viewport
- App shell architecture
- Push notifications
- Background sync
- Performance: Cache strategies

### 32. **Design a Mobile-First Responsive Layout**
**Question:** Create an adaptive layout system

**Answer Approach:**
- Breakpoint management
- Touch-optimized interactions
- Viewport considerations
- Performance budgets
- Critical CSS delivery
- Image optimization

## Performance & Optimization Questions

### 33. **Design a CDN Strategy for Static Assets**
**Question:** How would you optimize CDN usage for Netflix?

**Answer Approach:**
- Asset versioning and cache busting
- Geographic distribution
- Fallback strategies
- Performance monitoring
- Cost optimization
- Security considerations

### 34. **Design a Client-Side Caching System**
**Question:** Design a caching system - asked by Google, Amazon and Netflix

**Answer Approach:**
- Cache invalidation strategies
- Storage options (localStorage, IndexedDB)
- Memory management
- Cache warming
- Offline support
- Performance metrics

### 35. **Design a Rate Limiting System (Client-Side)**
**Question:** Distributed Rate Limiting

**Answer Approach:**
- Token bucket algorithm
- Request queuing
- Retry strategies
- User feedback
- Priority queues
- Performance impact

## Architecture & Framework Questions

### 36. **Design a Micro-Frontend Architecture**
**Question:** Build a system with multiple independent frontend apps

**Answer Approach:**
- Module federation strategies
- Shared dependencies management
- Communication between apps
- Routing coordination
- Build and deployment
- Performance: Code splitting

### 37. **Design a Plugin System**
**Question:** Create an extensible application architecture

**Answer Approach:**
- Plugin API design
- Sandboxing and security
- Lifecycle management
- Dependency resolution
- Version compatibility
- Performance isolation

### 38. **Design a State Management System**
**Question:** Build a Redux-like state management solution

**Answer Approach:**
- Unidirectional data flow
- Action dispatching
- Middleware system
- Time-travel debugging
- Performance: Selective updates
- DevTools integration

### 39. **Design a Component Library**
**Question:** Create a reusable UI component library

**Answer Approach:**
- Component libraries like Material UI, Chakra UI
- API consistency
- Theming system
- Documentation generation
- Testing strategies
- Tree-shaking support

### 40. **Design a Build System**
**Question:** Create a frontend build pipeline

**Answer Approach:**
- Module bundling strategies
- Code splitting logic
- Asset optimization
- Development vs production builds
- Caching strategies
- Performance metrics

## Security & Privacy Questions

### 41. **Design a Secure Authentication System**
**Question:** Implement secure login with 2FA

**Answer Approach:**
- Token storage strategies
- Session management
- CSRF protection
- XSS prevention
- OAuth integration
- Security headers

### 42. **Design a Permission System UI**
**Question:** Build role-based access control interface

**Answer Approach:**
- Permission visualization
- Role management
- Inheritance handling
- Audit logging UI
- Bulk operations
- Performance with large datasets

## Accessibility Questions

### 43. **Design an Accessible Form System**
**Question:** Create forms meeting WCAG standards

**Answer Approach:**
- Accessibility is what differentiates senior candidates from junior candidates
- ARIA labels and descriptions
- Keyboard navigation
- Error announcement
- Focus management
- Screen reader testing

### 44. **Design a Screen Reader Friendly SPA**
**Question:** Build a single-page app with full accessibility

**Answer Approach:**
- Route announcements
- Live regions
- Focus management
- Semantic HTML
- Skip navigation
- Testing strategies

## Advanced Technical Questions

### 45. **Design a Virtual DOM Implementation**
**Question:** Build a React-like virtual DOM system

**Answer Approach:**
- Diff algorithm design
- Reconciliation process
- Component lifecycle
- Event delegation
- Performance optimizations
- Fiber architecture concepts

### 46. **Design a GraphQL Client**
**Question:** Create a GraphQL client with caching

**Answer Approach:**
- Query normalization
- Cache management
- Optimistic updates
- Subscription handling
- Error boundaries
- Performance: Query batching

### 47. **Design a Service Worker Strategy**
**Question:** Implement comprehensive offline support

**Answer Approach:**
- Cache strategies (cache-first, network-first)
- Background sync
- Push notifications
- Update mechanisms
- Debugging tools
- Performance monitoring

### 48. **Design a WebAssembly Integration**
**Question:** Integrate WASM modules in a web app

**Answer Approach:**
- Module loading strategies
- Memory management
- JavaScript interop
- Performance profiling
- Use case selection
- Fallback strategies

### 49. **Design a Real-time Collaboration System**
**Question:** Build Google Docs-like collaboration

**Answer Approach:**
- CRDTs or Operational Transformation for conflict resolution
- Presence awareness
- Cursor synchronization
- Network optimization
- Offline support
- Performance at scale

### 50. **Design a Performance Monitoring System**
**Question:** Create client-side performance tracking

**Answer Approach:**
- Core Web Vitals tracking
- Custom metrics definition
- Data collection strategies
- Visualization dashboards
- Alert mechanisms
- Privacy considerations

### 51. **Design a Recommendation Engine UI**
**Question:** Build a personalized content recommendation interface

**Answer Approach:**
- Recommendation display strategies
- User preference learning
- A/B testing framework
- Feedback collection
- Privacy-preserving design
- Performance with large datasets

### 52. **Design a Multi-tenant SaaS Frontend**
**Question:** Create a white-label platform architecture

**Answer Approach:**
- Theming and branding system
- Feature flags implementation
- Tenant isolation
- Configuration management
- Performance optimization
- Security boundaries

## Interview Tips for Frontend System Design

Based on the RADIO framework:

1. **Requirements Exploration**: Always start by clarifying requirements and scope
2. **Architecture/High-level Design**: Identify key components and their relationships
3. **Data Model**: Define entities and data flow
4. **Interface Definition**: Specify APIs between components
5. **Optimization & Deep Dive**: Focus on performance, accessibility, security

Remember:
- Advanced topics like performance, accessibility and i18n differentiate senior candidates
- System design questions tend to be open ended and vague, leaving room to explore
- Focus on trade-offs and justify your decisions
- Consider both technical and user experience aspects
- Don't forget about error states and edge cases

This comprehensive list covers the most frequently asked frontend system design questions in MAANG and other big tech companies, providing a structured approach to tackle each type of question effectively.



I'll provide comprehensive answers for each frontend system design question. This will be an extensive guide with proper approaches for MAANG interviews.

# Complete Frontend System Design Interview Guide with Detailed Answers

## 1. Design a News Feed (Facebook/Twitter)

### Initial Questions to Ask:
- What type of content? (text, images, videos, links)
- Real-time updates or refresh-based?
- How many users and posts scale?
- Mobile-first or desktop-first?
- Personalized feed or chronological?

### Detailed Answer:

**High-Level Architecture:**
```
┌─────────────────┐     ┌──────────────┐     ┌─────────────┐
│   Feed UI       │────▶│  Feed API    │────▶│  WebSocket  │
│  Components     │     │  Gateway     │     │   Server    │
└─────────────────┘     └──────────────┘     └─────────────┘
        │                      │
        ▼                      ▼
┌─────────────────┐     ┌──────────────┐
│  Local State    │     │   CDN        │
│  (Redux/Zustand)│     │  (Images)    │
└─────────────────┘     └──────────────┘
```

**Core Components:**

```javascript
// 1. Feed Container Component
const FeedContainer = () => {
  const [posts, setPosts] = useState([]);
  const [hasMore, setHasMore] = useState(true);
  const [cursor, setCursor] = useState(null);
  const observerRef = useRef();

  // Intersection Observer for infinite scroll
  const lastPostRef = useCallback(node => {
    if (observerRef.current) observerRef.current.disconnect();
    observerRef.current = new IntersectionObserver(entries => {
      if (entries[0].isIntersecting && hasMore) {
        fetchMorePosts();
      }
    });
    if (node) observerRef.current.observe(node);
  }, [hasMore]);

  // WebSocket for real-time updates
  useEffect(() => {
    const ws = new WebSocket('wss://api.example.com/feed');
    ws.onmessage = (event) => {
      const newPost = JSON.parse(event.data);
      setPosts(prev => [newPost, ...prev]);
    };
    return () => ws.close();
  }, []);

  return (
    <VirtualList
      items={posts}
      renderItem={(post, index) => (
        <Post 
          key={post.id} 
          post={post}
          ref={index === posts.length - 1 ? lastPostRef : null}
        />
      )}
    />
  );
};

// 2. Virtual Scrolling Implementation
const VirtualList = ({ items, renderItem, itemHeight = 200 }) => {
  const [scrollTop, setScrollTop] = useState(0);
  const [viewportHeight, setViewportHeight] = useState(window.innerHeight);
  
  const startIndex = Math.floor(scrollTop / itemHeight);
  const endIndex = Math.ceil((scrollTop + viewportHeight) / itemHeight);
  const visibleItems = items.slice(startIndex, endIndex + 1);
  
  return (
    <div 
      style={{ height: items.length * itemHeight, position: 'relative' }}
      onScroll={(e) => setScrollTop(e.target.scrollTop)}
    >
      {visibleItems.map((item, index) => (
        <div 
          key={item.id}
          style={{
            position: 'absolute',
            top: (startIndex + index) * itemHeight,
            height: itemHeight
          }}
        >
          {renderItem(item, startIndex + index)}
        </div>
      ))}
    </div>
  );
};

// 3. Optimistic Updates for Likes/Comments
const useOptimisticUpdate = () => {
  const queryClient = useQueryClient();
  
  const likeMutation = useMutation({
    mutationFn: (postId) => api.likePost(postId),
    onMutate: async (postId) => {
      await queryClient.cancelQueries(['post', postId]);
      const previousPost = queryClient.getQueryData(['post', postId]);
      
      // Optimistically update
      queryClient.setQueryData(['post', postId], old => ({
        ...old,
        likes: old.likes + 1,
        isLiked: true
      }));
      
      return { previousPost };
    },
    onError: (err, postId, context) => {
      // Rollback on error
      queryClient.setQueryData(['post', postId], context.previousPost);
    }
  });
  
  return likeMutation;
};
```

**API Design:**
```javascript
// GraphQL Schema for efficient data fetching
const typeDefs = `
  type Post {
    id: ID!
    author: User!
    content: String!
    media: [Media]
    likes: Int!
    comments(first: Int, after: String): CommentConnection!
    createdAt: DateTime!
  }

  type Query {
    feed(first: Int!, after: String): PostConnection!
  }

  type Subscription {
    postAdded: Post!
    postUpdated(postId: ID!): Post!
  }
`;

// REST API Alternative
GET /api/feed?cursor=xxx&limit=20
WebSocket: /ws/feed for real-time updates
```

**Performance Optimizations:**
1. **Image Loading Strategy:**
```javascript
const LazyImage = ({ src, alt, placeholder }) => {
  const [imageSrc, setImageSrc] = useState(placeholder);
  const imgRef = useRef();

  useEffect(() => {
    const observer = new IntersectionObserver(
      ([entry]) => {
        if (entry.isIntersecting) {
          // Progressive loading: thumbnail → full image
          const img = new Image();
          img.src = src;
          img.onload = () => setImageSrc(src);
          observer.disconnect();
        }
      },
      { rootMargin: '50px' }
    );
    
    if (imgRef.current) observer.observe(imgRef.current);
    return () => observer.disconnect();
  }, [src]);

  return <img ref={imgRef} src={imageSrc} alt={alt} loading="lazy" />;
};
```

2. **Request Debouncing & Caching:**
```javascript
const useDebouncedInfiniteScroll = (fetchFn, delay = 300) => {
  const [loading, setLoading] = useState(false);
  const timeoutRef = useRef();

  const debouncedFetch = useCallback((...args) => {
    clearTimeout(timeoutRef.current);
    timeoutRef.current = setTimeout(async () => {
      setLoading(true);
      await fetchFn(...args);
      setLoading(false);
    }, delay);
  }, [fetchFn, delay]);

  return { debouncedFetch, loading };
};
```

**State Management:**
```javascript
// Normalized Redux State
const feedSlice = createSlice({
  name: 'feed',
  initialState: {
    posts: {
      byId: {},      // { postId: postData }
      allIds: [],    // [postId1, postId2, ...]
    },
    users: {
      byId: {}       // { userId: userData }
    },
    pagination: {
      cursor: null,
      hasMore: true
    }
  },
  reducers: {
    postsReceived: (state, action) => {
      action.payload.posts.forEach(post => {
        state.posts.byId[post.id] = post;
        state.posts.allIds.push(post.id);
        state.users.byId[post.author.id] = post.author;
      });
    }
  }
});
```

**Key Considerations:**
- **Offline Support:** Service workers for caching posts
- **Real-time Updates:** WebSocket with exponential backoff
- **Security:** XSS prevention, content sanitization
- **Accessibility:** Keyboard navigation, screen reader announcements

---

## 2. Design YouTube Video Player & Platform

### Initial Questions:
- Live streaming support needed?
- Quality adaptation (480p to 4K)?
- Comments real-time or paginated?
- Monetization (ads) required?
- Download/offline viewing?

### Detailed Answer:

**Architecture Overview:**
```
┌──────────────────┐     ┌─────────────────┐     ┌──────────────┐
│  Video Player    │────▶│  Streaming API  │────▶│  CDN/Origin  │
│  (HLS/DASH)      │     │  (Adaptive)     │     │   Servers    │
└──────────────────┘     └─────────────────┘     └──────────────┘
        │                         │
        ▼                         ▼
┌──────────────────┐     ┌─────────────────┐
│  Player Controls │     │  Analytics API   │
│  & UI Components │     │  (Watch time)    │
└──────────────────┘     └─────────────────┘
```

**Core Video Player Implementation:**
```javascript
// 1. Adaptive Bitrate Streaming Player
class AdaptiveVideoPlayer {
  constructor(videoElement, manifestUrl) {
    this.video = videoElement;
    this.hls = new Hls({
      maxBufferLength: 30,
      maxMaxBufferLength: 600,
      maxBufferSize: 60 * 1000 * 1000, // 60 MB
      enableWorker: true,
      lowLatencyMode: true
    });
    
    this.initializePlayer(manifestUrl);
    this.setupQualityControls();
    this.setupAnalytics();
  }

  initializePlayer(manifestUrl) {
    if (Hls.isSupported()) {
      this.hls.loadSource(manifestUrl);
      this.hls.attachMedia(this.video);
      
      // Auto quality switching based on bandwidth
      this.hls.on(Hls.Events.LEVEL_SWITCHED, (event, data) => {
        console.log(`Switched to quality: ${data.level}`);
      });
    } else if (this.video.canPlayType('application/vnd.apple.mpegurl')) {
      // Native HLS support (Safari)
      this.video.src = manifestUrl;
    }
  }

  setupQualityControls() {
    this.hls.on(Hls.Events.MANIFEST_PARSED, (event, data) => {
      const qualities = data.levels.map(level => ({
        height: level.height,
        bitrate: level.bitrate,
        label: `${level.height}p`
      }));
      
      this.renderQualitySelector(qualities);
    });
  }

  // Network-aware quality adaptation
  adaptQualityBasedOnNetwork() {
    if ('connection' in navigator) {
      const connection = navigator.connection;
      const effectiveType = connection.effectiveType;
      
      const qualityMap = {
        'slow-2g': 0,  // 144p
        '2g': 1,       // 240p
        '3g': 2,       // 360p
        '4g': 4        // 720p+
      };
      
      this.hls.currentLevel = qualityMap[effectiveType] || -1; // -1 for auto
    }
  }
}

// 2. Custom Video Controls Component
const VideoControls = ({ videoRef, duration }) => {
  const [currentTime, setCurrentTime] = useState(0);
  const [volume, setVolume] = useState(1);
  const [playbackRate, setPlaybackRate] = useState(1);
  const [isFullscreen, setIsFullscreen] = useState(false);
  const [showControls, setShowControls] = useState(true);
  const controlsTimeoutRef = useRef();

  // Auto-hide controls
  useEffect(() => {
    const hideControls = () => {
      clearTimeout(controlsTimeoutRef.current);
      setShowControls(true);
      
      controlsTimeoutRef.current = setTimeout(() => {
        if (!videoRef.current.paused) {
          setShowControls(false);
        }
      }, 3000);
    };

    videoRef.current.addEventListener('mousemove', hideControls);
    return () => {
      videoRef.current.removeEventListener('mousemove', hideControls);
    };
  }, []);

  // Keyboard shortcuts
  useEffect(() => {
    const handleKeyPress = (e) => {
      switch(e.key) {
        case ' ':
          e.preventDefault();
          videoRef.current.paused ? videoRef.current.play() : videoRef.current.pause();
          break;
        case 'ArrowLeft':
          videoRef.current.currentTime -= 10;
          break;
        case 'ArrowRight':
          videoRef.current.currentTime += 10;
          break;
        case 'f':
          toggleFullscreen();
          break;
        case 'm':
          videoRef.current.muted = !videoRef.current.muted;
          break;
      }
    };

    document.addEventListener('keydown', handleKeyPress);
    return () => document.removeEventListener('keydown', handleKeyPress);
  }, []);

  return (
    <div className={`controls ${showControls ? 'visible' : 'hidden'}`}>
      <ProgressBar 
        currentTime={currentTime}
        duration={duration}
        onSeek={(time) => videoRef.current.currentTime = time}
      />
      <div className="control-buttons">
        <PlayPauseButton videoRef={videoRef} />
        <VolumeControl 
          volume={volume}
          onChange={(v) => {
            setVolume(v);
            videoRef.current.volume = v;
          }}
        />
        <TimeDisplay currentTime={currentTime} duration={duration} />
        <PlaybackRateSelector 
          rate={playbackRate}
          onChange={(rate) => {
            setPlaybackRate(rate);
            videoRef.current.playbackRate = rate;
          }}
        />
        <QualitySelector />
        <FullscreenButton onClick={toggleFullscreen} />
      </div>
    </div>
  );
};

// 3. Thumbnail Preview on Hover
const ThumbnailPreview = ({ videoId, duration }) => {
  const [previewTime, setPreviewTime] = useState(0);
  const [thumbnailUrl, setThumbnailUrl] = useState('');

  const handleMouseMove = (e) => {
    const rect = e.currentTarget.getBoundingClientRect();
    const x = e.clientX - rect.left;
    const percentage = x / rect.width;
    const time = percentage * duration;
    
    setPreviewTime(time);
    // Thumbnail sprite sheet approach
    const spriteIndex = Math.floor(time / 10); // Every 10 seconds
    setThumbnailUrl(`/api/thumbnails/${videoId}/sprite-${spriteIndex}.jpg`);
  };

  return (
    <div className="thumbnail-preview" onMouseMove={handleMouseMove}>
      <img 
        src={thumbnailUrl} 
        style={{
          objectPosition: `${-(previewTime % 10) * 160}px 0px`
        }}
      />
      <span>{formatTime(previewTime)}</span>
    </div>
  );
};

// 4. Comments System with Real-time Updates
const CommentsSection = ({ videoId }) => {
  const [comments, setComments] = useState([]);
  const [sortBy, setSortBy] = useState('top'); // 'top' or 'newest'
  const wsRef = useRef();

  useEffect(() => {
    // WebSocket for real-time comments
    wsRef.current = new WebSocket(`wss://api.youtube.com/comments/${videoId}`);
    
    wsRef.current.onmessage = (event) => {
      const newComment = JSON.parse(event.data);
      setComments(prev => [newComment, ...prev]);
      
      // Show notification for new comment
      showNotification('New comment added');
    };

    return () => wsRef.current.close();
  }, [videoId]);

  // Virtualized comment list for performance
  return (
    <VirtualizedList
      items={comments}
      itemHeight={100}
      renderItem={(comment) => (
        <Comment 
          key={comment.id}
          comment={comment}
          onReply={handleReply}
          onLike={handleLike}
        />
      )}
    />
  );
};

// 5. Video Recommendations Algorithm
const VideoRecommendations = ({ currentVideoId, userId }) => {
  const recommendations = useQuery({
    queryKey: ['recommendations', currentVideoId, userId],
    queryFn: async () => {
      // Collaborative filtering + content-based
      const [userHistory, videoMetadata, trendingVideos] = await Promise.all([
        api.getUserWatchHistory(userId),
        api.getVideoMetadata(currentVideoId),
        api.getTrendingVideos()
      ]);

      return calculateRecommendations({
        userHistory,
        videoMetadata,
        trendingVideos,
        weights: {
          history: 0.4,
          similarity: 0.4,
          trending: 0.2
        }
      });
    },
    staleTime: 5 * 60 * 1000 // 5 minutes
  });

  return (
    <div className="recommendations">
      {recommendations.data?.map(video => (
        <VideoCard 
          key={video.id}
          video={video}
          prefetch={true} // Prefetch video metadata
        />
      ))}
    </div>
  );
};
```

**Performance Optimizations:**
```javascript
// 1. Video Prefetching Strategy
const usePrefetchVideo = () => {
  const prefetchVideo = useCallback((videoUrl) => {
    if ('requestIdleCallback' in window) {
      requestIdleCallback(() => {
        const link = document.createElement('link');
        link.rel = 'prefetch';
        link.as = 'video';
        link.href = videoUrl;
        document.head.appendChild(link);
      });
    }
  }, []);

  return prefetchVideo;
};

// 2. Bandwidth Detection and Adaptation
class BandwidthMonitor {
  constructor() {
    this.measurements = [];
    this.avgBandwidth = 0;
  }

  measureBandwidth(bytesLoaded, timeElapsed) {
    const bandwidth = (bytesLoaded * 8) / (timeElapsed / 1000); // bits per second
    this.measurements.push(bandwidth);
    
    // Keep last 10 measurements
    if (this.measurements.length > 10) {
      this.measurements.shift();
    }
    
    this.avgBandwidth = this.measurements.reduce((a, b) => a + b, 0) / this.measurements.length;
    return this.avgBandwidth;
  }

  getSuggestedQuality() {
    const qualityThresholds = {
      '144p': 200000,    // 200 Kbps
      '240p': 400000,    // 400 Kbps
      '360p': 800000,    // 800 Kbps
      '480p': 1500000,   // 1.5 Mbps
      '720p': 3000000,   // 3 Mbps
      '1080p': 6000000,  // 6 Mbps
      '4K': 15000000     // 15 Mbps
    };

    for (const [quality, threshold] of Object.entries(qualityThresholds).reverse()) {
      if (this.avgBandwidth >= threshold) {
        return quality;
      }
    }
    return '144p';
  }
}

// 3. Analytics and Watch Time Tracking
const useVideoAnalytics = (videoId, userId) => {
  const watchTimeRef = useRef(0);
  const lastUpdateRef = useRef(0);
  
  const sendAnalytics = useCallback(async (eventType, data) => {
    await api.analytics.track({
      eventType,
      videoId,
      userId,
      timestamp: Date.now(),
      ...data
    });
  }, [videoId, userId]);

  const trackWatchTime = useCallback((currentTime) => {
    const timeDiff = currentTime - lastUpdateRef.current;
    
    // Only count if reasonable time difference (not seeking)
    if (timeDiff > 0 && timeDiff < 2) {
      watchTimeRef.current += timeDiff;
    }
    
    lastUpdateRef.current = currentTime;
    
    // Send analytics every 10 seconds
    if (Math.floor(watchTimeRef.current) % 10 === 0) {
      sendAnalytics('watch_time', { 
        duration: watchTimeRef.current,
        position: currentTime
      });
    }
  }, [sendAnalytics]);

  return { trackWatchTime, sendAnalytics };
};
```

**Key Considerations:**
- **DRM Support:** Widevine/FairPlay integration for protected content
- **Accessibility:** Closed captions, audio descriptions, keyboard navigation
- **Mobile Optimization:** Gesture controls, picture-in-picture
- **Error Handling:** Retry logic, fallback qualities
- **SEO:** Server-side rendering for video metadata

---

## 3. Design an E-commerce Marketplace (Amazon/eBay)

### Initial Questions:
- Product catalog size?
- Real-time inventory updates?
- Multiple sellers support?
- International shipping/currencies?
- Payment methods required?

### Detailed Answer:

**Architecture Overview:**
```
┌────────────────┐     ┌─────────────────┐     ┌──────────────┐
│ Product Search │────▶│ Search Service  │────▶│ Elasticsearch│
│   & Filters    │     │  (Algolia/ES)   │     │   Cluster    │
└────────────────┘     └─────────────────┘     └──────────────┘
        │                      │                         │
        ▼                      ▼                         ▼
┌────────────────┐     ┌─────────────────┐     ┌──────────────┐
│  Shopping Cart │────▶│  Cart Service   │────▶│  Redis Cache │
│   Component    │     │   (Real-time)   │     │              │
└────────────────┘     └─────────────────┘     └──────────────┘
        │                      │
        ▼                      ▼
┌────────────────┐     ┌─────────────────┐
│    Checkout    │────▶│ Payment Gateway │
│     Flow       │     │    (Stripe)     │
└────────────────┘     └─────────────────┘
```

**Core Components Implementation:**

```javascript
// 1. Product Search with Faceted Filtering
const ProductSearch = () => {
  const [searchParams, setSearchParams] = useSearchParams();
  const [filters, setFilters] = useState({
    category: searchParams.get('category') || '',
    priceRange: [0, 10000],
    brands: [],
    ratings: 0,
    shipping: 'all'
  });

  // Elasticsearch query builder
  const buildSearchQuery = useCallback(() => {
    return {
      query: {
        bool: {
          must: [
            searchParams.get('q') ? {
              multi_match: {
                query: searchParams.get('q'),
                fields: ['title^3', 'description', 'brand^2', 'category'],
                fuzziness: 'AUTO'
              }
            } : { match_all: {} }
          ],
          filter: [
            filters.category && { term: { category: filters.category } },
            {
              range: {
                price: {
                  gte: filters.priceRange[0],
                  lte: filters.priceRange[1]
                }
              }
            },
            filters.brands.length > 0 && { terms: { brand: filters.brands } },
            filters.ratings > 0 && { range: { rating: { gte: filters.ratings } } }
          ].filter(Boolean)
        }
      },
      aggs: {
        categories: { terms: { field: 'category' } },
        brands: { terms: { field: 'brand' } },
        price_ranges: {
          range: {
            field: 'price',
            ranges: [
              { to: 50 },
              { from: 50, to: 100 },
              { from: 100, to: 500 },
              { from: 500 }
            ]
          }
        }
      },
      sort: getSortCriteria(searchParams.get('sort')),
      from: (currentPage - 1) * pageSize,
      size: pageSize
    };
  }, [filters, searchParams]);

  // Debounced search
  const debouncedSearch = useMemo(
    () => debounce((query) => {
      setSearchParams(prev => ({...prev, q: query}));
    }, 300),
    []
  );

  return (
    <div className="search-container">
      <SearchBar onChange={debouncedSearch} />
      <div className="search-results-layout">
        <FilterSidebar 
          filters={filters}
          onChange={setFilters}
          aggregations={searchResults.aggregations}
        />
        <ProductGrid 
          products={searchResults.hits}
          loading={isLoading}
        />
      </div>
    </div>
  );
};

// 2. Shopping Cart with Real-time Sync
const ShoppingCart = () => {
  const { user } = useAuth();
  const queryClient = useQueryClient();
  
  // Local storage for guest users
  const [localCart, setLocalCart] = useLocalStorage('cart', []);
  
  // Real-time cart sync for logged-in users
  const { data: serverCart, isLoading } = useQuery({
    queryKey: ['cart', user?.id],
    queryFn: () => api.cart.get(user.id),
    enabled: !!user,
    staleTime: 0, // Always fetch fresh data
  });

  // WebSocket for real-time updates
  useEffect(() => {
    if (!user) return;
    
    const ws = new WebSocket(`wss://api.shop.com/cart/${user.id}`);
    
    ws.onmessage = (event) => {
      const update = JSON.parse(event.data);
      queryClient.setQueryData(['cart', user.id], update);
    };

    return () => ws.close();
  }, [user, queryClient]);

  // Optimistic updates
  const updateCartItem = useMutation({
    mutationFn: ({ productId, quantity }) => 
      api.cart.updateItem(user?.id || 'guest', productId, quantity),
    onMutate: async ({ productId, quantity }) => {
      await queryClient.cancelQueries(['cart', user?.id]);
      
      const previousCart = queryClient.getQueryData(['cart', user?.id]);
      
      // Optimistic update
      queryClient.setQueryData(['cart', user?.id], old => ({
        ...old,
        items: old.items.map(item =>
          item.productId === productId
            ? { ...item, quantity }
            : item
        ).filter(item => item.quantity > 0),
        total: calculateTotal(old.items)
      }));

      return { previousCart };
    },
    onError: (err, variables, context) => {
      queryClient.setQueryData(['cart', user?.id], context.previousCart);
      toast.error('Failed to update cart');
    }
  });

  // Cart persistence strategy
  const cart = user ? serverCart : localCart;

  return (
    <CartProvider value={{ cart, updateCartItem }}>
      <CartUI />
    </CartProvider>
  );
};

// 3. Inventory Management with Real-time Updates
const InventoryTracker = ({ productId, onOutOfStock }) => {
  const [inventory, setInventory] = useState(null);
  const [reserved, setReserved] = useState(0);

  useEffect(() => {
    // Server-Sent Events for inventory updates
    const eventSource = new EventSource(
      `/api/inventory/stream/${productId}`
    );

    eventSource.onmessage = (event) => {
      const data = JSON.parse(event.data);
      setInventory(data.available);
      
      if (data.available <= 0) {
        onOutOfStock();
      }
    };

    return () => eventSource.close();
  }, [productId]);

  // Reserve inventory when adding to cart
  const reserveInventory = async (quantity) => {
    try {
      const response = await api.inventory.reserve({
        productId,
        quantity,
        duration: 15 * 60 * 1000 // 15 minutes reservation
      });
      
      setReserved(prev => prev + quantity);
      
      // Auto-release after timeout
      setTimeout(() => {
        setReserved(prev => prev - quantity);
      }, response.duration);
      
      return response.reservationId;
    } catch (error) {
      if (error.code === 'INSUFFICIENT_INVENTORY') {
        throw new Error('Not enough items in stock');
      }
      throw error;
    }
  };

  return { inventory, reserved, reserveInventory };
};

// 4. Checkout Flow with Payment Integration
const CheckoutFlow = () => {
  const [step, setStep] = useState(0);
  const [checkoutData, setCheckoutData] = useState({
    shipping: null,
    billing: null,
    payment: null,
    orderSummary: null
  });

  const steps = [
    { component: ShippingAddress, key: 'shipping' },
    { component: PaymentMethod, key: 'payment' },
    { component: OrderReview, key: 'review' },
    { component: OrderConfirmation, key: 'confirmation' }
  ];

  // Payment processing with 3D Secure
  const processPayment = async (paymentMethod) => {
    const stripe = await loadStripe(process.env.STRIPE_PUBLIC_KEY);
    
    try {
      // Create payment intent
      const { clientSecret, requiresAction } = await api.payments.createIntent({
        amount: checkoutData.orderSummary.total,
        currency: 'usd',
        paymentMethodId: paymentMethod.id
      });

      if (requiresAction) {
        // 3D Secure authentication
        const result = await stripe.confirmCardPayment(clientSecret);
        
        if (result.error) {
          throw new Error(result.error.message);
        }
      }

      // Confirm order
      const order = await api.orders.create({
        ...checkoutData,
        paymentIntentId: clientSecret
      });

      return order;
    } catch (error) {
      // Handle payment failures
      await api.payments.cancelIntent(clientSecret);
      throw error;
    }
  };

  return (
    <CheckoutProvider value={{ checkoutData, setCheckoutData }}>
      <ProgressIndicator currentStep={step} steps={steps} />
      <AnimatePresence mode="wait">
        <motion.div
          key={step}
          initial={{ opacity: 0, x: 20 }}
          animate={{ opacity: 1, x: 0 }}
          exit={{ opacity: 0, x: -20 }}
        >
          {React.createElement(steps[step].component, {
            onNext: () => setStep(step + 1),
            onBack: () => setStep(step - 1)
          })}
        </motion.div>
      </AnimatePresence>
    </CheckoutProvider>
  );
};

// 5. Product Image Gallery with Zoom
const ProductImageGallery = ({ images }) => {
  const [selectedImage, setSelectedImage] = useState(0);
  const [zoomPosition, setZoomPosition] = useState({ x: 0, y: 0 });
  const [showZoom, setShowZoom] = useState(false);

  const handleMouseMove = (e) => {
    const rect = e.currentTarget.getBoundingClientRect();
    const x = (e.clientX - rect.left) / rect.width;
    const y = (e.clientY - rect.top) / rect.height;
    setZoomPosition({ x, y });
  };

  return (
    <div className="image-gallery">
      <div className="thumbnails">
        {images.map((image, index) => (
          <Thumbnail
            key={image.id}
            src={image.thumbnail}
            selected={index === selectedImage}
            onClick={() => setSelectedImage(index)}
          />
        ))}
      </div>
      
      <div 
        className="main-image"
        onMouseEnter={() => setShowZoom(true)}
        onMouseLeave={() => setShowZoom(false)}
        onMouseMove={handleMouseMove}
      >
        <img 
          src={images[selectedImage].large}
          alt={images[selectedImage].alt}
        />
        
        {showZoom && (
          <div 
            className="zoom-lens"
            style={{
              left: `${zoomPosition.x * 100}%`,
              top: `${zoomPosition.y * 100}%`
            }}
          />
        )}
      </div>
      
      {showZoom && (
        <div className="zoom-preview">
          <img 
            src={images[selectedImage].xlarge}
            style={{
              transform: `translate(-${zoomPosition.x * 100}%, -${zoomPosition.y * 100}%)`
            }}
          />
        </div>
      )}
    </div>
  );
};
```

**Performance Optimizations:**
```javascript
// 1. Image Optimization Pipeline
const useOptimizedImages = (images) => {
  return images.map(image => ({
    ...image,
    srcset: `
      ${image.url}?w=400 400w,
      ${image.url}?w=800 800w,
      ${image.url}?w=1200 1200w
    `,
    sizes: '(max-width: 640px) 100vw, (max-width: 1024px) 50vw, 33vw',
    loading: 'lazy'
  }));
};

// 2. Search Results Caching
const useSearchCache = () => {
  const cache = useRef(new Map());
  
  const getCachedResults = (query) => {
    const key = JSON.stringify(query);
    const cached = cache.current.get(key);
    
    if (cached && Date.now() - cached.timestamp < 5 * 60 * 1000) {
      return cached.data;
    }
    
    return null;
  };

  const setCachedResults = (query, data) => {
    const key = JSON.stringify(query);
    cache.current.set(key, {
      data,
      timestamp: Date.now()
    });
    
    // Limit cache size
    if (cache.current.size > 50) {
      const firstKey = cache.current.keys().next().value;
      cache.current.delete(firstKey);
    }
  };

  return { getCachedResults, setCachedResults };
};

// 3. Predictive Prefetching
const usePredictivePrefetch = () => {
  const prefetchProduct = useCallback((productId) => {
    // Prefetch product data
    queryClient.prefetchQuery({
      queryKey: ['product', productId],
      queryFn: () => api.products.get(productId),
      staleTime: 10 * 60 * 1000
    });

    // Prefetch related products
    queryClient.prefetchQuery({
      queryKey: ['related-products', productId],
      queryFn: () => api.products.getRelated(productId)
    });
  }, []);

  // Prefetch on hover
  const onProductHover = (productId) => {
    requestIdleCallback(() => prefetchProduct(productId));
  };

  return { onProductHover };
};
```

**Key Considerations:**
- **Security:** PCI compliance, secure payment tokenization
- **Internationalization:** Multi-currency, tax calculation, shipping zones
- **Performance:** CDN for images, search result caching
- **Accessibility:** Form validation, keyboard navigation
- **SEO:** Structured data, server-side rendering

---

## 4. Design Instagram/Photo Sharing App

### Initial Questions:
- Photo/video or just photos?
- Stories feature required?
- Real-time features (likes, comments)?
- Filters and editing tools?
- Direct messaging included?

### Detailed Answer:

**Architecture Overview:**
```
┌────────────────┐     ┌─────────────────┐     ┌──────────────┐
│  Photo Upload  │────▶│ Upload Service  │────▶│  S3 Bucket   │
│   Component    │     │ (Presigned URLs)│     │  (Original)  │
└────────────────┘     └─────────────────┘     └──────────────┘
        │                      │                      │
        ▼                      ▼                      ▼
┌────────────────┐     ┌─────────────────┐     ┌──────────────┐
│ Image Process  │────▶│ Lambda Functions│────▶│  CDN Cache   │
│   (Filters)    │     │ (Sharp/ImageMgk)│     │ (Optimized)  │
└────────────────┘     └─────────────────┘     └──────────────┘
```

**Core Implementation:**

```javascript
// 1. Image Upload with Progress and Optimization
const ImageUploader = () => {
  const [uploadProgress, setUploadProgress] = useState({});
  const [processingStatus, setProcessingStatus] = useState({});

  const processImage = async (file) => {
    // Client-side image optimization
    const compressedImage = await compressImage(file, {
      maxWidth: 1080,
      maxHeight: 1080,
      quality: 0.8,
      format: 'webp'
    });

    // Generate blurhash for placeholder
    const blurhash = await generateBlurhash(compressedImage);
    
    return { compressedImage, blurhash };
  };

  const uploadImage = async (file, metadata) => {
    const uploadId = generateId();
    
    try {
      // Get presigned URL for direct S3 upload
      const { uploadUrl, imageId } = await api.getUploadUrl({
        contentType: file.type,
        size: file.size
      });

      // Process image before upload
      const { compressedImage, blurhash } = await processImage(file);

      // Upload to S3 with progress tracking
      await axios.put(uploadUrl, compressedImage, {
        headers: {
          'Content-Type': compressedImage.type,
        },
        onUploadProgress: (progressEvent) => {
          const percentCompleted = Math.round(
            (progressEvent.loaded * 100) / progressEvent.total
          );
          setUploadProgress(prev => ({
            ...prev,
            [uploadId]: percentCompleted
          }));
        }
      });

      // Save metadata
      const post = await api.posts.create({
        imageId,
        caption: metadata.caption,
        location: metadata.location,
        tags: metadata.tags,
        blurhash
      });

      // Trigger server-side processing
      await api.images.process(imageId, {
        sizes: ['thumbnail', 'medium', 'large'],
        formats: ['webp', 'jpg']
      });

      return post;
    } catch (error) {
      handleUploadError(error, uploadId);
    }
  };

  return {
    uploadImage,
    uploadProgress,
    processingStatus
  };
};

// 2. Instagram-style Image Filters
const ImageFilters = ({ imageUrl, onFilterApply }) => {
  const canvasRef = useRef();
  const [selectedFilter, setSelectedFilter] = useState('normal');

  const filters = {
    normal: { brightness: 1, contrast: 1, saturate: 1 },
    clarendon: { brightness: 1.2, contrast: 1.1, saturate: 1.35 },
    gingham: { brightness: 1.05, contrast: 1.1, hueRotate: -10 },
    moon: { brightness: 1.1, contrast: 1.1, saturate: 0 },
    lark: { brightness: 1.2, contrast: 0.9, saturate: 1.1 },
    reyes: { brightness: 1.1, contrast: 0.85, saturate: 0.75 },
    juno: { brightness: 1.15, contrast: 1.2, saturate: 1.4 },
    slumber: { brightness: 1.05, saturate: 0.66 },
    crema: { brightness: 1.1, contrast: 0.9, saturate: 0.8 },
    ludwig: { brightness: 1.05, contrast: 1.05, saturate: 1.1 },
    aden: { brightness: 1.2, contrast: 0.85, saturate: 0.85, hueRotate: 20 },
    perpetua: { brightness: 1.05, contrast: 1.1, saturate: 1.1 }
  };

  const applyFilter = useCallback((filterName) => {
    const canvas = canvasRef.current;
    const ctx = canvas.getContext('2d');
    const img = new Image();
    
    img.onload = () => {
      canvas.width = img.width;
      canvas.height = img.height;
      
      // Apply CSS filters to canvas
      const filter = filters[filterName];
      ctx.filter = Object.entries(filter)
        .map(([key, value]) => {
          switch(key) {
            case 'brightness':
            case 'contrast':
            case 'saturate':
              return `${key}(${value})`;
            case 'hueRotate':
              return `hue-rotate(${value}deg)`;
            default:
              return '';
          }
        })
        .join(' ');
      
      ctx.drawImage(img, 0, 0);
      
      // Convert to blob for upload
      canvas.toBlob((blob) => {
        onFilterApply(blob, filterName);
      }, 'image/jpeg', 0.9);
    };
    
    img.src = imageUrl;
  }, [filters, imageUrl, onFilterApply]);

  return (
    <div className="filter-selector">
      <canvas ref={canvasRef} style={{ display: 'none' }} />
      <div className="filter-preview-list">
        {Object.keys(filters).map(filterName => (
          <FilterPreview
            key={filterName}
            name={filterName}
            imageUrl={imageUrl}
            filter={filters[filterName]}
            selected={selectedFilter === filterName}
            onClick={() => {
              setSelectedFilter(filterName);
              applyFilter(filterName);
            }}
          />
        ))}
      </div>
    </div>
  );
};

// 3. Infinite Scroll Feed with Virtualization
const InstagramFeed = () => {
  const [posts, setPosts] = useState([]);
  const [hasMore, setHasMore] = useState(true);
  const observerTarget = useRef(null);

  // Intersection Observer for infinite scroll
  useEffect(() => {
    const observer = new IntersectionObserver(
      entries => {
        if (entries[0].isIntersecting && hasMore) {
          loadMorePosts();
        }
      },
      { threshold: 0.1, rootMargin: '100px' }
    );

    if (observerTarget.current) {
      observer.observe(observerTarget.current);
    }

    return () => observer.disconnect();
  }, [hasMore]);

  // Virtual scrolling for performance
  const rowRenderer = ({ index, key, style }) => {
    const post = posts[index];
    
    return (
      <div key={key} style={style}>
        <PostCard 
          post={post}
          onDoubleTap={handleLike}
          onComment={handleComment}
        />
      </div>
    );
  };

  return (
    <div className="instagram-feed">
      <Stories />
      <AutoSizer>
        {({ height, width }) => (
          <List
            height={height}
            width={width}
            rowCount={posts.length}
            rowHeight={cache.rowHeight}
            rowRenderer={rowRenderer}
            overscanRowCount={3}
          />
        )}
      </AutoSizer>
      <div ref={observerTarget} className="loading-trigger" />
    </div>
  );
};

// 4. Stories Implementation
const Stories = () => {
  const [stories, setStories] = useState([]);
  const [currentStory, setCurrentStory] = useState(null);
  const [progress, setProgress] = useState(0);

  const StoryViewer = ({ story, onClose, onNext, onPrevious }) => {
    const [currentIndex, setCurrentIndex] = useState(0);
    const progressInterval = useRef();

    useEffect(() => {
      // Auto-advance after 5 seconds
      progressInterval.current = setInterval(() => {
        setProgress(prev => {
          if (prev >= 100) {
            handleNext();
            return 0;
          }
          return prev + (100 / 50); // 5 seconds = 50 intervals of 100ms
        });
      }, 100);

      return () => clearInterval(progressInterval.current);
    }, [currentIndex]);

    const handleNext = () => {
      if (currentIndex < story.items.length - 1) {
        setCurrentIndex(currentIndex + 1);
        setProgress(0);
      } else {
        onNext();
      }
    };

    const handlePrevious = () => {
      if (currentIndex > 0) {
        setCurrentIndex(currentIndex - 1);
        setProgress(0);
      } else {
        onPrevious();
      }
    };

    return (
      <div className="story-viewer">
        <div className="progress-bars">
          {story.items.map((_, idx) => (
            <div key={idx} className="progress-bar">
              <div 
                className="progress-fill"
                style={{
                  width: idx < currentIndex ? '100%' 
                    : idx === currentIndex ? `${progress}%` 
                    : '0%'
                }}
              />
            </div>
          ))}
        </div>
        
        <TouchHandler
          onSwipeLeft={handleNext}
          onSwipeRight={handlePrevious}
          onTap={(e) => {
            const { clientX } = e;
            const { width } = e.currentTarget.getBoundingClientRect();
            clientX < width / 2 ? handlePrevious() : handleNext();
          }}
        >
          <img 
            src={story.items[currentIndex].url}
            alt={story.items[currentIndex].alt}
            loading="eager"
          />
        </TouchHandler>
        
        <button onClick={onClose} className="close-button">×</button>
      </div>
    );
  };

  return (
    <div className="stories-container">
      <div className="stories-list">
        {stories.map(story => (
          <StoryThumbnail
            key={story.id}
            story={story}
            onClick={() => setCurrentStory(story)}
            hasNewStory={story.hasNew}
          />
        ))}
      </div>
      
      {currentStory && (
        <Modal>
          <StoryViewer
            story={currentStory}
            onClose={() => setCurrentStory(null)}
            onNext={() => {
              const currentIndex = stories.findIndex(s => s.id === currentStory.id);
              if (currentIndex < stories.length - 1) {
                setCurrentStory(stories[currentIndex + 1]);
              } else {
                setCurrentStory(null);
              }
            }}
            onPrevious={() => {
              const currentIndex = stories.findIndex(s => s.id === currentStory.id);
              if (currentIndex > 0) {
                setCurrentStory(stories[currentIndex - 1]);
              }
            }}
          />
        </Modal>
      )}
    </div>
  );
};

// 5. Real-time Engagement Features
const EngagementFeatures = ({ postId }) => {
  const [likes, setLikes] = useState(0);
  const [isLiked, setIsLiked] = useState(false);
  const [showHeart, setShowHeart] = useState(false);

  // Optimistic like with animation
  const handleDoubleTap = useCallback(() => {
    if (!isLiked) {
      setIsLiked(true);
      setLikes(prev => prev + 1);
      setShowHeart(true);
      
      // Animate heart
      setTimeout(() => setShowHeart(false), 1000);
      
      // Send to server
      api.posts.like(postId).catch(() => {
        // Rollback on error
        setIsLiked(false);
        setLikes(prev => prev - 1);
      });
    }
  }, [isLiked, postId]);

  // Real-time updates via WebSocket
  useEffect(() => {
    const ws = new WebSocket(`wss://api.instagram.com/posts/${postId}/engagement`);
    
    ws.onmessage = (event) => {
      const { type, data } = JSON.parse(event.data);
      
      switch(type) {
        case 'like':
          setLikes(data.totalLikes);
          break;
        case 'comment':
          // Handle new comment notification
          break;
      }
    };

    return () => ws.close();
  }, [postId]);

  return (
    <div className="engagement-section">
      <div className="actions">
        <HeartButton 
          isLiked={isLiked}
          onClick={() => handleDoubleTap()}
        />
        <CommentButton onClick={openComments} />
        <ShareButton onClick={handleShare} />
        <SaveButton isSaved={isSaved} onClick={handleSave} />
      </div>
      
      {showHeart && (
        <AnimatedHeart className="double-tap-heart" />
      )}
      
      <div className="likes-count">
        {likes.toLocaleString()} likes
      </div>
    </div>
  );
};
```

**Performance Optimizations:**
```javascript
// 1. Image Loading Strategy
const useProgressiveImageLoading = (src, blurhash) => {
  const [imageSrc, setImageSrc] = useState(null);
  const [isLoading, setIsLoading] = useState(true);

  useEffect(() => {
    // Show blurhash immediately
    if (blurhash) {
      const pixels = decode(blurhash, 32, 32);
      const canvas = document.createElement('canvas');
      canvas.width = 32;
      canvas.height = 32;
      const ctx = canvas.getContext('2d');
      const imageData = ctx.createImageData(32, 32);
      imageData.data.set(pixels);
      ctx.putImageData(imageData, 0, 0);
      setImageSrc(canvas.toDataURL());
    }

    // Load actual image
    const img = new Image();
    img.src = src;
    img.onload = () => {
      setImageSrc(src);
      setIsLoading(false);
    };
  }, [src, blurhash]);

  return { imageSrc, isLoading };
};

// 2. Smart Preloading
const useSmartPreloading = () => {
  const preloadQueue = useRef(new Set());
  
  const preloadImage = useCallback((url, priority = 'low') => {
    if (preloadQueue.current.has(url)) return;
    
    preloadQueue.current.add(url);
    
    if (priority === 'high') {
      const link = document.createElement('link');
      link.rel = 'preload';
      link.as = 'image';
      link.href = url;
      document.head.appendChild(link);
    } else {
      requestIdleCallback(() => {
        new Image().src = url;
      });
    }
  }, []);

  return preloadImage;
};

// 3. Memory Management for Large Feeds
const useMemoryManagement = (items, windowSize = 10) => {
  const [visibleRange, setVisibleRange] = useState([0, windowSize]);
  const observerRefs = useRef({});

  useEffect(() => {
    const observers = {};
    
    items.forEach((item, index) => {
      if (index < visibleRange[0] - 5 || index > visibleRange[1] + 5) {
        // Clear images outside visible range
        if (observerRefs.current[item.id]) {
          URL.revokeObjectURL(observerRefs.current[item.id]);
          delete observerRefs.current[item.id];
        }
      }
    });
    
    return () => {
      Object.values(observerRefs.current).forEach(url => {
        URL.revokeObjectURL(url);
      });
    };
  }, [items, visibleRange]);

  return { visibleRange, setVisibleRange };
};
```

**Key Considerations:**
- **Image Processing:** Client-side compression, format conversion
- **Real-time Features:** WebSocket for likes, comments, presence
- **Performance:** Lazy loading, virtualization, image caching
- **Accessibility:** Alt text, keyboard navigation
- **Privacy:** Story view tracking, private accounts

---

## 5. Design a Chat/Messaging Application (WhatsApp/Messenger)

### Initial Questions:
- One-to-one or group chat support?
- End-to-end encryption required?
- Voice/video calling features?
- Message types (text, media, files)?
- Read receipts and typing indicators?

### Detailed Answer:

**Architecture Overview:**
```
┌────────────────┐     ┌─────────────────┐     ┌──────────────┐
│   Chat UI      │────▶│ WebSocket Server│────▶│ Message Queue│
│  Components    │◀────│   (Socket.io)   │◀────│   (Redis)    │
└────────────────┘     └─────────────────┘     └──────────────┘
        │                      │                      │
        ▼                      ▼                      ▼
┌────────────────┐     ┌─────────────────┐     ┌──────────────┐
│  IndexedDB     │     │   REST API      │     │   Database   │
│ (Offline Store)│     │   (HTTP/2)      │     │ (PostgreSQL) │
└────────────────┘     └─────────────────┘     └──────────────┘
```

**Core Implementation:**

```javascript
// 1. Real-time Messaging Engine
class MessagingEngine {
  constructor() {
    this.socket = null;
    this.messageQueue = [];
    this.connected = false;
    this.reconnectAttempts = 0;
    this.handlers = new Map();
  }

  connect(userId, authToken) {
    this.socket = io('wss://chat.app.com', {
      auth: { token: authToken },
      transports: ['websocket'],
      reconnection: true,
      reconnectionDelay: 1000,
      reconnectionDelayMax: 5000,
      reconnectionAttempts: Infinity
    });

    this.setupEventHandlers();
    this.setupHeartbeat();
  }

  setupEventHandlers() {
    this.socket.on('connect', () => {
      this.connected = true;
      this.reconnectAttempts = 0;
      this.flushMessageQueue();
      this.emit('connection:established');
    });

    this.socket.on('disconnect', (reason) => {
      this.connected = false;
      this.emit('connection:lost', reason);
    });

    this.socket.on('message:new', this.handleNewMessage.bind(this));
    this.socket.on('message:delivered', this.handleDeliveryReceipt.bind(this));
    this.socket.on('message:read', this.handleReadReceipt.bind(this));
    this.socket.on('user:typing', this.handleTypingIndicator.bind(this));
    this.socket.on('user:presence', this.handlePresenceUpdate.bind(this));
  }

  sendMessage(message) {
    const messageWithMetadata = {
      ...message,
      id: generateMessageId(),
      timestamp: Date.now(),
      status: 'sending'
    };

    if (this.connected) {
      this.socket.emit('message:send', messageWithMetadata, (ack) => {
        if (ack.success) {
          this.updateMessageStatus(messageWithMetadata.id, 'sent');
        } else {
          this.handleSendFailure(messageWithMetadata);
        }
      });
    } else {
      // Queue message for later delivery
      this.messageQueue.push(messageWithMetadata);
      this.emit('message:queued', messageWithMetadata);
    }

    return messageWithMetadata;
  }

  flushMessageQueue() {
    while (this.messageQueue.length > 0) {
      const message = this.messageQueue.shift();
      this.sendMessage(message);
    }
  }
}

// 2. Chat UI Component with Virtual Scrolling
const ChatWindow = ({ conversationId }) => {
  const [messages, setMessages] = useState([]);
  const [isTyping, setIsTyping] = useState(false);
  const [typingUsers, setTypingUsers] = useState(new Set());
  const messagesEndRef = useRef(null);
  const virtualScroller = useRef(null);

  // Message grouping by time and sender
  const groupedMessages = useMemo(() => {
    return messages.reduce((groups, message, index) => {
      const prevMessage = messages[index - 1];
      const shouldGroup = prevMessage && 
        prevMessage.senderId === message.senderId &&
        message.timestamp - prevMessage.timestamp < 60000; // 1 minute

      if (shouldGroup) {
        groups[groups.length - 1].messages.push(message);
      } else {
        groups.push({
          senderId: message.senderId,
          timestamp: message.timestamp,
          messages: [message]
        });
      }

      return groups;
    }, []);
  }, [messages]);

  // Infinite scroll for message history
  const loadMoreMessages = useCallback(async () => {
    const oldestMessage = messages[0];
    if (!oldestMessage) return;

    const olderMessages = await api.messages.getBefore(
      conversationId,
      oldestMessage.timestamp,
      50
    );

    setMessages(prev => [...olderMessages, ...prev]);
  }, [conversationId, messages]);

  // Typing indicator with debounce
  const sendTypingIndicator = useMemo(
    () => debounce(() => {
      messagingEngine.socket.emit('user:typing', {
        conversationId,
        isTyping: true
      });

      // Stop typing after 3 seconds
      setTimeout(() => {
        messagingEngine.socket.emit('user:typing', {
          conversationId,
          isTyping: false
        });
      }, 3000);
    }, 500),
    [conversationId]
  );

  return (
    <div className="chat-window">
      <VirtualizedMessageList
        ref={virtualScroller}
        messages={groupedMessages}
        onScrollTop={loadMoreMessages}
        renderMessage={(messageGroup) => (
          <MessageGroup
            key={messageGroup.timestamp}
            messages={messageGroup.messages}
            senderId={messageGroup.senderId}
          />
        )}
      />
      
      {typingUsers.size > 0 && (
        <TypingIndicator users={Array.from(typingUsers)} />
      )}
      
      <MessageInput
        onSend={handleSendMessage}
        onTyping={sendTypingIndicator}
        onFileSelect={handleFileUpload}
      />
    </div>
  );
};

// 3. End-to-End Encryption Implementation
class E2EEncryption {
  constructor() {
    this.keyPairs = new Map();
    this.sessionKeys = new Map();
  }

  async generateKeyPair() {
    const keyPair = await crypto.subtle.generateKey(
      {
        name: 'RSA-OAEP',
        modulusLength: 2048,
        publicExponent: new Uint8Array([1, 0, 1]),
        hash: 'SHA-256',
      },
      true,
      ['encrypt', 'decrypt']
    );

    return keyPair;
  }

  async encryptMessage(message, recipientPublicKey) {
    // Generate session key for this message
    const sessionKey = await crypto.subtle.generateKey(
      {
        name: 'AES-GCM',
        length: 256,
      },
      true,
      ['encrypt', 'decrypt']
    );

    // Encrypt message with session key
    const iv = crypto.getRandomValues(new Uint8Array(12));
    const encodedMessage = new TextEncoder